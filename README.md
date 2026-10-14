# Hermes Desktop AppImage

Unofficial Linux x86-64 AppImage builds of
[Nous Research Hermes Desktop](https://github.com/NousResearch/hermes-agent).

This repository is a downstream packaging and update channel. It is not
affiliated with, endorsed by, or maintained by Nous Research.

## Releases

The scheduled workflow checks the official upstream
`/NousResearch/hermes-agent/releases/latest` endpoint once per day. When a
new stable release appears, it:

1. checks out the exact upstream release tag;
2. builds only `apps/desktop` on a disposable GitHub-hosted Linux runner;
3. packages the x86-64 AppImage;
4. verifies that the AppImage's embedded install stamp matches the resolved
   upstream commit;
5. publishes the AppImage, SHA-256 checksum, and provenance record to a GitHub
   Release.

The release filename is deliberately stable:
`Hermes-Desktop-x86_64.AppImage`. This lets Gear Lever follow the latest
release without embedding credentials or a private URL.

## Gear Lever

Use these update-source settings:

- Manager: `GithubUpdater`
- Repository: `gffranco/hermes-desktop-appimage`
- Release filename: `Hermes-Desktop-x86_64.AppImage`
- Pre-releases: disabled

Gear Lever can check automatically and notify when a release changes. Applying
the update remains a user-confirmed operation in Gear Lever.

## Verification

Each release includes:

- `Hermes-Desktop-x86_64.AppImage`
- `Hermes-Desktop-x86_64.AppImage.sha256`
- `Hermes-Desktop-build-provenance.json`

Verify a downloaded release with:

```sh
sha256sum -c Hermes-Desktop-x86_64.AppImage.sha256
```

The AppImage itself can report its embedded source stamp at startup. The
workflow additionally extracts and checks that stamp before publishing.

## License and trademarks

Hermes Agent is licensed under the MIT License; the upstream license notice is
preserved in [LICENSE](LICENSE). Hermes and Nous Research names and artwork
belong to their respective owners and are used here only to identify the
packaged upstream software.
