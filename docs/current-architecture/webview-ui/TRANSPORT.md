# UI Transport

**Status:** STATIC REVIEWED — runtime behavior not exercised.  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8

## 1. Interface and composition

The UI consumes the MessageTransport interface re-exported from core through src/transport/types.ts. src/transport/index.ts creates one module-level singleton:

- VS Code runtime: PostMessageTransport.
- Standalone browser runtime: WebSocketTransport pointed at the current origin's /ws endpoint.
- Standalone development mode: the WebSocket connection is not opened; browserMock messages are injected through WebSocketTransport.deliver().

The browser mock is development-only and is not the production server. The transport captures the server token in the WebSocket URL at creation so reconnects retain the handshake token even if the address bar is later cleared.

## 2. VS Code postMessage transport

File: src/transport/postMessageTransport.ts

- Sends ClientMessage objects through acquireVsCodeApi().postMessage().
- Receives by registering a window message listener.
- Incoming event.data is cast to ServerMessage without runtime schema validation.
- Reports CONNECTED for its lifetime; ready is already resolved and onStateChange returns a no-op disposer.
- dispose() does nothing because this implementation owns no transport-level resource.

Host lifecycle and trust boundary remain in adapters/vscode/PixelAgentsViewProvider.ts. The webview transport itself does not authenticate or validate message payloads.

## 3. Standalone WebSocket transport

File: src/transport/webSocketTransport.ts

State:
- Initial state CONNECTING.
- CONNECTED on socket open.
- RECONNECTING after close/retry.
- DISCONNECTED after dispose.

Behavior:
- Exponential reconnect delay is delegated to reconnectBackoff.ts.
- Client messages sent while the socket is not OPEN are queued in pendingMessages and flushed on open.
- Incoming frames are JSON.parse'd then cast to ServerMessage.
- onMessage subscriptions are held in an array and unsubscribe filters out the handler.
- dispose() marks the transport disposed, clears reconnect timer, closes socket, clears message handlers and pending queue, transitions state and clears state handlers.
- ready resolves on the first successful open; later reconnects do not create a new ready promise.

## 4. Development browser mock

File: src/transport/index.ts and src/browserMock.ts

In Vite DEV, index.ts installs a window message listener and forwards object messages with a string type to WebSocketTransport.deliver(). It deliberately does not call connect(), because Vite has no Pixel Agents /ws endpoint and would otherwise reconnect repeatedly.

deliver() calls handlers directly and does not share the onmessage try/catch. Consequently, a subscriber exception can behave differently between browser-mock delivery and a real WebSocket frame.

## 5. Reconnect and application reconciliation

Reconnect delay policy lives in reconnectBackoff.ts. onReconnect.ts is a small helper used by the application hook. Transport reconnect alone does not restore the UI's domain state: useExtensionMessages coordinates webviewReady, initial state, existing-agent reconciliation, and subsequent snapshots. Terminal raw-frame reconnect is separate from this control-plane transport.

The VS Code postMessage transport has no reconnect state. Its handshake/readiness semantics are instead coordinated by PixelAgentsViewProvider's webviewReady buffer and initial snapshot ordering.

## 6. Findings

### WV-TR-01 — Subscriber exception aborts WebSocket dispatch (Medium)

In WebSocketTransport.onmessage, JSON parsing and iteration over all handlers occur in one try/catch. If a handler throws, the loop stops and remaining handlers do not receive the message. The exception is swallowed by the catch intended for malformed JSON. deliver() does not have the same catch boundary.

### Validation gap — incoming ServerMessage casts

Both WebSocketTransport and PostMessageTransport cast untrusted runtime data to ServerMessage without comprehensive schema validation. The DEV browser mock checks only that data is an object with a string type. This is not sufficient to establish payload shape.

### Queue semantics

WebSocketTransport queues **every** outgoing ClientMessage while disconnected and flushes the entire pending array on the next open. There is no command classification, expiry, coalescing, acknowledgement, cancellation or queue bound; see **WV-RESYNC-02** in DEEP-AUDIT. On reconnect, the `onReconnect` state callback sends `webviewReady` before `onopen` flushes the pending array, so the new snapshot is requested before queued commands replay. The separate `TerminalConnection.write()` path does not buffer terminal input while its raw socket is unavailable; it retains only the last geometry and re-sends resize on reconnect. These are intentionally distinct channels and must not be conflated.

## 7. Ownership and disposal questions

- The singleton transport is created at module import time; app-level teardown/disposal ownership should be traced against each runtime entrypoint.
- PostMessageTransport has no disposal work, but subscriptions remove their own window listeners.
- WebSocketTransport.dispose clears its own timers/socket/subscribers/queue.
- The DEV window listener installed by transport/index.ts is not retained as an explicit disposer; current module-singleton lifetime may make this acceptable, but hot reload/reinitialization behavior has not been exercised.
- Subscriber callbacks and state callbacks are invoked synchronously; exceptions may interrupt dispatch to later callbacks.

## 8. Verification boundary

Static source review only. GOV-WEBVIEW-012 adds a static cross-layer lifecycle trace and WV-RESYNC-02; no browser, VS Code host, WebSocket server, reconnect fault injection, malformed-frame test or runtime lifecycle experiment was performed. Tests have not been executed.
