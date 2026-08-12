<div align="center">
  <img src="assets/images/icon.png" alt="Whisper Phone icon" width="112" />

  # Whisper Phone

  Privacy-first messaging, encrypted voice notes, and peer-to-peer calls for iOS and Android.

  [![CI](https://github.com/salarzeidanlou/whisper-phone/actions/workflows/ci.yml/badge.svg)](https://github.com/salarzeidanlou/whisper-phone/actions/workflows/ci.yml)
  [![Expo SDK 54](https://img.shields.io/badge/Expo_SDK-54-000020?logo=expo)](https://expo.dev/)
  [![License: MIT](https://img.shields.io/badge/License-MIT-22c55e.svg)](LICENSE)
</div>

Whisper Phone is an open-source Expo and React Native client for the
[Whisper server](https://github.com/theany-org/whisper-server). Text and voice
messages are encrypted on the sender's device and decrypted on the recipient's
device. The backend relays ciphertext and WebRTC signaling.

> [!WARNING]
> This project is under active development and has not received an independent
> security audit. Public keys currently use trust on first use and are not
> verified with fingerprints or safety numbers. Do not rely on Whisper Phone
> for high-risk communications without performing your own security review.

## Highlights

- End-to-end encrypted one-to-one text and voice messages with TweetNaCl
- Peer-to-peer WebRTC audio calls with STUN/TURN support
- Private keys and session tokens stored through Expo SecureStore
- Short-lived, single-use tickets for WebSocket authentication
- Reply threads, retry states, presence, and offline message delivery
- Optional on-device SQLite message history
- Runtime-selectable server for self-hosted deployments
- Dark, mobile-first interface with Expo Router and React Native

## How message encryption works

```text
Sender                         Whisper server                    Recipient
------                         --------------                    ---------
plaintext
   │
   ▼
NaCl box ── ciphertext + nonce ──► relay / offline queue ───────────► NaCl box
                                                                       │
                                                                       ▼
                                                                   plaintext
```

Each installation generates a Curve25519 key pair. The private key is kept in
the platform credential store through `expo-secure-store`; the public key is
registered with the server. Message payloads use TweetNaCl's authenticated box
construction (X25519 + XSalsa20-Poly1305) with a fresh 24-byte nonce.

The server can still observe metadata such as accounts, connection times,
message routing, and ciphertext size. Because public keys are not independently
verified, the current protocol does not protect against a malicious server
substituting keys. See [Security](#security) for the full limitations.

## Tech stack

| Area | Technology |
| --- | --- |
| App | Expo 54, React Native 0.81, React 19 |
| Language | TypeScript 5.9 |
| Navigation | Expo Router 6 |
| State | Zustand 5 |
| Messaging crypto | TweetNaCl |
| Calls | react-native-webrtc |
| Realtime | WebSocket |
| Local storage | Expo SecureStore, Expo SQLite |
| UI | Uniwind, Tailwind CSS, Reanimated |

## Requirements

- Node.js 20 or newer
- pnpm 10 (Corepack is recommended)
- Android Studio or Xcode, or an EAS development build
- A running [Whisper server](https://github.com/theany-org/whisper-server)

> [!IMPORTANT]
> This project uses native modules such as `react-native-webrtc` and
> `react-native-incall-manager`. It does not run fully in Expo Go. Use a native
> development build.

## Getting started

1. Clone and enter the repository:

   ```bash
   git clone https://github.com/salarzeidanlou/whisper-phone.git
   cd whisper-phone
   ```

2. Enable pnpm and install dependencies:

   ```bash
   corepack enable
   pnpm install --frozen-lockfile
   ```

3. Create your local environment file:

   ```bash
   cp .env.example .env
   ```

   Set the URL of your backend:

   ```env
   EXPO_PUBLIC_WHISPER_API_URL=https://your-server.example.com
   ```

4. Build and run the native app:

   ```bash
   pnpm android
   # or, on macOS:
   pnpm ios
   ```

After the first native build, start Metro for the development client with
`pnpm start`. You can also change the server from the login screen.

## Quality checks

```bash
pnpm check
```

This runs ESLint and TypeScript's no-emit type check. The same checks run for
pull requests and pushes to `main` through GitHub Actions.

## Production builds

Install and authenticate the EAS CLI, connect the project to your own Expo
account if needed, and run:

```bash
npx eas-cli build --platform android --profile production
npx eas-cli build --platform ios --profile production
```

The `production-apk` profile in `eas.json` can be used when a directly
installable Android APK is needed:

```bash
npx eas-cli build --platform android --profile production-apk
```

## Project layout

```text
app/                 Expo Router screens and navigation
components/          Reusable messaging, recording, and call UI
crypto/              Key lifecycle and NaCl encryption helpers
services/            HTTP, WebSocket, audio, calls, notifications, SQLite
store/               Zustand auth, chat, call, presence, and settings stores
types/               Shared TypeScript contracts
utils/               Small formatting helpers
assets/              App icons and splash artwork
```

## Server configuration

`EXPO_PUBLIC_WHISPER_API_URL` provides the initial API base URL. If it is not
set, the app currently defaults to `https://whisper.theany.ir`. Users can
override the value at runtime; the selected URL is stored with SecureStore.

For a self-hosted backend, follow the
[Whisper server setup guide](https://github.com/theany-org/whisper-server#readme).
Use HTTPS in production so HTTP requests and WebSocket signaling are protected
in transit.

## Security

Current protections include authenticated message encryption, local credential
storage, encrypted voice-note payloads, and single-use WebSocket tickets.

Known security and privacy limitations:

- No public-key fingerprint or safety-number verification
- No audited protocol or application implementation
- No per-message ratchet; session key rotation is not forward secrecy
- The server can observe communication metadata and relay signaling
- Local message history, when enabled, is not additionally encrypted by the app
- No background delivery when the app is fully terminated
- Offline messages expire according to the server's queue policy

Please report suspected vulnerabilities privately according to
[SECURITY.md](SECURITY.md).

## Contributing

Contributions are welcome. Read [CONTRIBUTING.md](CONTRIBUTING.md), use the issue
templates, and run `pnpm check` before opening a pull request.

## License

Whisper Phone is available under the [MIT License](LICENSE).
