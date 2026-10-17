# Offline P2P Chat — Developer Spec (KuaiLiao / Bolo Chat)

## What the client wants (in his own words)
Two nearby phones connect **without internet** — like Zapya — and exchange
text messages, pictures, videos and files, within Bluetooth/Wi-Fi range.

## UX flow
1. User opens "Nearby Chat" (آس پاس چیٹ) screen and taps "Find nearby".
2. App shows a list of nearby phones running the same app (device name / username).
3. User taps a device → both phones show a pairing prompt → Accept.
4. Chat window opens. Messages, photos, videos, files send directly phone-to-phone.
5. Clear "Offline" indicator in the chat header (e.g. grey/blue badge "آف لائن").
6. When a phone goes out of range, show "Out of range — reconnecting…" and
   auto-reconnect when back in range. Queue unsent messages and retry.

## Technical approach (Android, native)
- **Do NOT use pure Bluetooth for file/video transfer** — classic Bluetooth
  (~2 Mbps) is too slow. Industry standard (Zapya, SHAREit, Files by Google):
  **Bluetooth/BLE for discovery + Wi-Fi Direct (Wi-Fi P2P) for transfer**
  (~100+ Mbps, ~100 m range).
- **China / Huawei build:** use **HMS Core Nearby Service** (discovery +
  file transfer). Do NOT use Google Play Services Nearby Connections API —
  it does not exist on Huawei phones sold in China.
- **International / GMS build:** Google Nearby Connections API (P2P_STAR or
  P2P_CLUSTER strategy) is acceptable.
- Permissions to request at runtime: BLUETOOTH_CONNECT, BLUETOOTH_SCAN,
  BLUETOOTH_ADVERTISE, ACCESS_FINE_LOCATION, NEARBY_WIFI_DEVICES
  (Android 12+), plus storage/media permissions for file sharing.
- Transport security: encrypt the P2P channel (TLS/Noise); never send plaintext.
- Message types: text, image, video, audio/voice note, document. Reuse the
  same chat-bubble UI as online chat so it feels like one app.
- Show transfer progress bars for files/videos; support cancel/resume.
- Battery: stop advertising/discovery when the Nearby screen is closed;
  use a foreground service with a persistent notification only while a
  Nearby session is active.

## Out of scope (say so explicitly, do not silently add)
- Mesh multi-hop relay (phone A → B → C). Nice-to-have later, not v1.
- Internet fallback inside the same chat (online + offline are separate modes in v1).

## Acceptance test (must be done on TWO REAL phones)
1. Both phones: airplane mode ON, Wi-Fi/Bluetooth ON, no internet.
2. Discover → pair → send text (must arrive < 3 s at 10 m distance).
3. Send a 50 MB video → must complete via Wi-Fi Direct, progress shown.
4. Move phones ~120 m apart → "out of range" state; bring back → auto-reconnect.
5. One phone kills the app → other phone shows offline state, no crash.
6. Repeat test on a Huawei phone without Google services (HMS build).

## Notes for the China/Huawei AppGallery release
- Backend for ONLINE chat must not be Firebase in the China build
  (Google is blocked in mainland China) — use AppGallery Connect / HMS
  or a China-hosted backend. Offline P2P needs no backend at all.
- App must handle China's app-store compliance (real-name verification,
  ICP filing) — confirm with the publisher before submission.
