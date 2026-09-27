---
icon: lucide/book-a
---

# Glossary

Attract screen
:   The looping idle screen a kiosk shows when nobody is using it. The idle watchdog returns the UI here after 90 seconds without input.

Error boundary
:   A React component that catches render errors and shows a "Tap to restart" screen instead of a blank page.

Health gate
:   An automated check that compares a new release against the previous one before it can be promoted to the next ring.

Kiosk mode
:   A Chromium launch mode that runs full screen with no browser UI, no address bar and no exit gestures.

Offline queue
:   Orders stored in IndexedDB while the kiosk can't reach the Kiosk API. They sync automatically once it reconnects.

Ring
:   A group of kiosks that receives a web release at the same time. Releases move from `ring-0` through `ring-3`.

Service worker
:   A browser background script that caches the app shell so the UI can load offline, and that proxies API calls to the offline queue.

ZenFleet
:   The device management platform: an agent on each kiosk, a control plane, and the [`kioskctl`](kioskctl.md) CLI.
