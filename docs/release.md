# Release & Homebrew Tap Maintenance

## How releases work

The `Publish Release` GitHub Actions workflow handles everything:

1. Bumps `.version` (patch, minor, or major)
2. Reuses existing `make build` / `make release-minor` / `make release-major` targets
3. Builds the release binary on `macos-26`
4. Regenerates `Sources/BuildInfo.swift` and updates the README version badge
5. Commits the release files and pushes the Git tag
6. Publishes `apfel-<version>-arm64-macos.tar.gz` on GitHub Releases
7. Rewrites and pushes `Formula/apfel.rb` in `Arthur-Ficial/homebrew-tap`

Do not hand-edit `Arthur-Ficial/homebrew-tap` for normal releases.

## One-time setup

Add the `HOMEBREW_TAP_PUSH_TOKEN` secret to `Arthur-Ficial/apfel`:

1. Create a fine-grained GitHub token with **Contents: Read and write** access to `Arthur-Ficial/homebrew-tap`
2. Store it:
   ```bash
   gh secret set HOMEBREW_TAP_PUSH_TOKEN --repo Arthur-Ficial/apfel
   ```

## Publishing a release

1. Open **Actions** in `Arthur-Ficial/apfel`
2. Run **Publish Release**
3. Choose `patch`, `minor`, or `major`

## Validation

After the workflow completes:

```bash
brew update
brew tap Arthur-Ficial/tap
brew reinstall Arthur-Ficial/tap/apfel
brew test Arthur-Ficial/tap/apfel
brew audit --strict Arthur-Ficial/tap/apfel
```

## Local builds

`make build` and `make install` still handle the normal auto-version bump and local release build. The tap is only updated by the publish workflow when a release is actually published (because the formula needs the final published asset SHA).
