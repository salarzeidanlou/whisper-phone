# Contributing to Whisper Phone

Thank you for helping improve Whisper Phone. Bug reports, documentation fixes,
tests, and focused feature contributions are welcome.

## Before you start

- Search existing issues before opening a new one.
- Open an issue before starting a large or security-sensitive change.
- Report vulnerabilities privately as described in [SECURITY.md](SECURITY.md).

## Development workflow

1. Fork the repository and create a branch from `main`.
2. Install Node.js 20 or newer and enable Corepack.
3. Install dependencies with `pnpm install --frozen-lockfile`.
4. Copy `.env.example` to `.env` and configure a Whisper server.
5. Run a native development build with `pnpm android` or `pnpm ios`.
6. Before opening a pull request, run `pnpm check`.

Keep pull requests small and explain the user-facing impact. Add or update
documentation when behavior or configuration changes.

## Commit and pull request guidance

Use clear, imperative commit messages. Pull requests should describe what
changed, why it changed, how it was tested, and any security or compatibility
considerations.

By contributing, you agree that your contribution is licensed under the MIT
License used by this repository.
