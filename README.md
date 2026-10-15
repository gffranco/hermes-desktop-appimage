# Hermes Desktop AppImage

Unofficial Linux x86-64 AppImage builds of
[Nous Research Hermes Desktop](https://github.com/NousResearch/hermes-agent).

This repository is a downstream packaging and update channel. It is not
affiliated with, endorsed by, or maintained by Nous Research.

New upstream releases are built automatically within a day, from the exact
upstream release tag, on GitHub-hosted runners.

## Which AppImage

Each release has two AppImages:

| File | For |
|---|---|
| `Hermes-Desktop-x86_64.AppImage` | The full app. It can run a local Hermes agent as well as connect to a remote one. |
| `Hermes-Light-x86_64.AppImage` | Upstream's Light client, which only connects to a remote Hermes: no local agent and no Python. |

Light is a separate application: it is named "Hermes Light" and keeps its
settings in `~/.config/Hermes Light`. Both can be installed side by side, but
switching from one to the other means adding your remote connection again.
Upstream builds Light but does not publish or test it, so treat it as the less
proven of the two.

Under Settings → Updates, the full app may report "No Python interpreter is
available to check the source update channel". It is harmless: that check
expects a Hermes CLI installed from source, and updates come from this
repository instead.

## Updates with Gear Lever

Install the AppImage in [Gear Lever](https://flathub.org/apps/it.mijorus.gearlever)
and leave its update source unset. Each AppImage carries this repository's
update information, so Gear Lever finds new releases on its own, and keeps
finding them after you reinstall the app. Other AppImage update tools, such as
AppImageUpdate, read the same information.

To set the update source by hand instead:

- Manager: `GithubUpdater`
- Repository: `gffranco/hermes-desktop-appimage`
- Release filename: `Hermes-Desktop-x86_64.AppImage` or
  `Hermes-Light-x86_64.AppImage`
- Pre-releases: disabled

Gear Lever can check automatically and notify when a release changes. Applying
the update remains a user-confirmed operation in Gear Lever.

## Verification

Next to each AppImage, a release includes its SHA-256 checksum and a provenance
record naming the upstream tag and commit it was built from. Verify a download
with:

```sh
sha256sum -c Hermes-Desktop-x86_64.AppImage.sha256
```

Upstream's source is built unmodified. The packaging only adds the release
version number and the update information, both before the checksum is taken.

## License and trademarks

Hermes Agent is licensed under the MIT License; the upstream license notice is
preserved in [LICENSE](LICENSE). Hermes and Nous Research names and artwork
belong to their respective owners and are used here only to identify the
packaged upstream software.
