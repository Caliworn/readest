# Personal Windows build

Based on upstream commit `8a4640d59d749fc88a54fbd0adcd10429775aa76`.

This fork makes third-party cloud synchronization available on all account
plans. It retains upstream account authentication, service URLs, Microsoft
OAuth configuration, and all other entitlement checks. It does not change
server-issued account plans or official service quotas.

The built-in updater is disabled and its official update endpoints are
removed. Updater signature artifacts are disabled so local builds do not
require the upstream signing key.

## Build Windows x64

Install Node.js, pnpm, Rust (MSVC), and Visual Studio 2022 Build Tools with the
Desktop development with C++ workload and Windows SDK. From the repository root:

```powershell
git submodule update --init --recursive
pnpm install --frozen-lockfile
pnpm --filter @readest/readest-app setup-vendors
pnpm tauri build --target x86_64-pc-windows-msvc --bundles nsis
```

The installer is produced under `target/x86_64-pc-windows-msvc/release/bundle/nsis`.
Do not copy `.env.local.example` unchanged: its placeholders override the
working default account and API configuration.

## Validation

```powershell
pnpm --filter @readest/readest-app test --run src/__tests__/components/settings/cloudSync.test.ts src/__tests__/services/sync/cloudSyncProvider.test.ts src/__tests__/services/sync/providers/onedrive
```

Real account sign-in, OAuth consent, token persistence after restart, and
two-device synchronization must also be checked interactively.

This initial build retains the upstream application identifier and OAuth
schemes. Back up the existing library before installation; treat it as a
replacement build rather than a side-by-side installation. Upgrades are manual.
An independently registered Microsoft application can be selected at build
time through `NEXT_PUBLIC_MICROSOFT_CLIENT_ID`. Its redirect URI must match
`readest-onedrive://auth`; its app folder must not be assumed to be the same as
the official application's OneDrive app folder.

The upstream AGPL-3.0 license continues to apply; retain the license and provide
the corresponding modified source when distributing this build.
