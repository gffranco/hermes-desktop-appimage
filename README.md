# Hermes Desktop AppImage

Unofficial Linux x86-64 AppImage builds of
[Nous Research Hermes Desktop](https://github.com/NousResearch/hermes-agent).

This repository is a downstream packaging and update channel. It is not
affiliated with, endorsed by, or maintained by Nous Research.

## Variants

Each release carries two AppImages built from the same upstream commit:

| File | Variant |
|---|---|
| `Hermes-Desktop-x86_64.AppImage` | The full app. It can run a local Hermes agent as well as connect to a remote one. |
| `Hermes-Light-x86_64.AppImage` | Upstream's remote-only Light client (`HERMES_DESKTOP_VARIANT=light`): no local agent and no Python. |

Light is a separate application to the desktop: it is named "Hermes Light"
and keeps its settings in `~/.config/Hermes Light`, so both can be installed
side by side, and switching from one to the other means adding your remote
connection again. Upstream builds Light but does not publish or test it, so
treat it as the less proven of the two.

The full app's in-app update check expects a Hermes CLI installed from source
at `~/.hermes/hermes-agent`. Without one it reports "No Python interpreter is
available to check the source update channel"; that message is harmless, as
updates come from this repository instead. Light declares its updates as
managed outside the app and shows no such error.

## Releases

The scheduled workflow checks the official upstream
`/NousResearch/hermes-agent/releases/latest` endpoint once per day and accepts
stable tags in either of upstream's schemes (`v2026.9.24`, `v0.22.1`). For each
variant the release does not carry yet, it:

1. checks out the exact upstream release tag;
2. builds only `apps/desktop` on a disposable GitHub-hosted Linux runner;
3. packages the x86-64 AppImage, stamped with the release version (upstream's
   `package.json` says `0.0.0`, which AppImage managers would otherwise show);
4. verifies that the AppImage's embedded install stamp matches the resolved
   upstream commit, tag and variant, and that its desktop entry carries the
   release version;
5. embeds AppImage update information pointing at this repository and
   generates the matching `.zsync` file (see [Gear Lever](#gear-lever));
6. publishes the AppImage, its `.zsync`, SHA-256 checksum, and provenance record
   to a GitHub Release.

The variants build in parallel. If one fails, the other still publishes and the
next scheduled run retries the missing one.

The workflow can also be run by hand from the Actions tab:

- `hermes_ref` builds a specific upstream tag instead of the latest.
- `rebuild` rebuilds every variant and replaces the files of an existing
  release, for refreshing a release after a workflow change.

The release filenames are deliberately stable, so update tools can follow the
latest release without embedding credentials or a private URL.

### Upstream workarounds

Upstream does not build Linux desktop packages yet, and its Linux packaging
path has two bugs this workflow works around:

- `npm run builder` runs without `--x64`. Since v0.21, an explicit architecture
  flag makes upstream's wrapper stage native dependencies into
  `build/native-deps-linux-x64`, which its own output guard rejects. Without the
  flag it targets the runner's architecture (x64) and reuses `build/native-deps`.
- A `{ "type": "commonjs" }` `package.json` is placed in
  `apps/desktop/build/prepared-packaging-tools`. Upstream copies
  electron-builder's CommonJS icon tool there, under a package declaring
  `"type": "module"`, so Node would load it as ESM and fail on `require()`.

If a build breaks after an upstream release, these are the first places to
look.

## Gear Lever

Each AppImage embeds GitHub update information in its `.upd_info` section, for
example:

```
gh-releases-zsync|gffranco|hermes-desktop-appimage|latest|Hermes-Desktop-x86_64.AppImage.zsync
```

Gear Lever reads it when an app has no update source configured, finds the
matching `.zsync` in the latest release, and compares its SHA-1 with the
installed file. So installing an AppImage from this repository is enough: leave
the update source unset. Because the information lives in the file, it also
survives reinstalling an AppImage over an existing entry, which clears a
manually set source. Other AppImage update tools, such as AppImageUpdate, read
the same section.

To set the source by hand instead (it takes precedence over the embedded
information):

- Manager: `GithubUpdater`
- Repository: `gffranco/hermes-desktop-appimage`
- Release filename: `Hermes-Desktop-x86_64.AppImage` or
  `Hermes-Light-x86_64.AppImage`
- Pre-releases: disabled

Gear Lever can check automatically and notify when a release changes. Applying
the update remains a user-confirmed operation in Gear Lever.

## Verification

Each release includes, for each variant (`Hermes-Desktop` or `Hermes-Light`):

- `<variant>-x86_64.AppImage`
- `<variant>-x86_64.AppImage.zsync`
- `<variant>-x86_64.AppImage.sha256`
- `<variant>-build-provenance.json`

Verify a downloaded release with:

```sh
sha256sum -c Hermes-Desktop-x86_64.AppImage.sha256
```

The AppImage itself can report its embedded source stamp at startup. The
workflow additionally extracts and checks that stamp before publishing. The
provenance record is a copy of it; its `payload` field is `bootstrap` for the
full app and `light` for Light.

The workflow changes the published AppImage in one place: it writes the update
information into the runtime's reserved `.upd_info` section. The checksum and
`.zsync` are computed afterwards, so both describe the published file.

## License and trademarks

Hermes Agent is licensed under the MIT License; the upstream license notice is
preserved in [LICENSE](LICENSE). Hermes and Nous Research names and artwork
belong to their respective owners and are used here only to identify the
packaged upstream software.
