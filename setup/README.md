# rellog setup GitHub Action

`tooppoo/rellog-actions/setup` installs the `rellog` CLI by running the repository's [`install.sh`](../install.sh) with the resolved version, which downloads the release archive, verifies it against the same release's `checksums.txt`, and extracts the binary.

## Usage

Check out the caller repository before running the action so the runner has the repository's `.rellog/` directory and `CHANGELOG.md`.

```yaml
- uses: tooppoo/rellog-actions/setup@v0.1.0
```

If the action is not referenced by a version tag, set the CLI version explicitly:

```yaml
- uses: tooppoo/rellog-actions/setup@main
  with:
    version: v0.1.2
```

## Inputs

| Input | Required | Default | Description |
| --- | --- | --- | --- |
| `version` | no | | GitHub Release tag of the `rellog` CLI to install. |

## Version resolution

When `version` is set, the action installs `rellog` from that GitHub Release tag.

When `version` is omitted, the action uses `github.action_ref` only if it is a version tag such as `v0.1.0`.
The action never falls back to `latest`. If the CLI version cannot be resolved, the action fails and asks the caller to set `version`.

The resolved release tag is passed as `install.sh --version <tag>`, which resolves the platform archive, downloads it from the matching GitHub Release, and verifies it against that release's `checksums.txt`.

## Supported runners

The action supports the same platforms as [`install.sh`](../install.sh):

| `runner.os` | `runner.arch` |
| --- | --- |
| `Linux` | `X64` |
| `Linux` | `ARM64` |
| `macOS` | `X64` |
| `macOS` | `ARM64` |

`Windows` runners are not supported. Other runner OS or architecture combinations fail before any archive download.

## Failure behavior

The action fails before running `rellog ready` when:

- `version` is omitted and the action ref is not a version tag;
- `install.sh` fails to install the resolved version, for example because:
  - the selected GitHub Release or platform archive does not exist;
  - the runner OS or architecture is unsupported;
  - archive download fails;
  - `checksums.txt` is missing, unreadable, malformed, missing the selected archive, or has more than one entry for the selected archive;
  - the archive checksum does not match `checksums.txt`;
  - archive extraction fails;
