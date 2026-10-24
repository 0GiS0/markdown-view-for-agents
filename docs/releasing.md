# Releasing

## CI

This repository includes a GitHub Actions workflow called `🧪 CI` that runs on every push to `main` and on every pull request.

It currently does two checks:

- Lints all PHP files against PHP `7.4`, `8.0`, `8.1`, `8.2`, and `8.3`
- Builds the installable plugin ZIP and uploads it as a workflow artifact

## Releases

This repository also includes a release workflow called `🚀 Release`, triggered by Git tags that start with `v`, for example:

```bash
git tag v1.0.1
git push origin v1.0.1
```

When you push a matching tag, GitHub Actions will:

- Build `dist/returngis-markdown-view-for-ai-agents.zip`
- Build a categorized changelog from merged pull requests
- Create a GitHub Release automatically
- Attach the plugin ZIP to that release

For consistency, keep the plugin header version, the `MD_FOR_AGENTS_VERSION` constant, and the `Stable tag` in `readme.txt` aligned with the tag you publish.

Before any WordPress.org submission or marketplace-facing release, search the directory for the proposed plugin name and slug to avoid collisions with existing listings.

## WordPress.org

The plugin is published at <https://wordpress.org/plugins/returngis-markdown-view-for-ai-agents/> from the SVN repository <https://plugins.svn.wordpress.org/returngis-markdown-view-for-ai-agents>.

SVN layout:

- `trunk/`: the latest plugin code, built from `package-plugin.sh`
- `tags/X.Y.Z/`: one copy per released version (without the `v` prefix)
- `assets/`: directory listing images (banners and icons) from the top level of `.wordpress-org/`; the SVG sources in `.wordpress-org/source/` are not uploaded

### Required secrets

Add these repository secrets in GitHub (Settings > Secrets and variables > Actions):

- `SVN_USERNAME`: `0GiS0` (case sensitive)
- `SVN_PASSWORD`: the SVN password generated in the "Account & Security" section of the WordPress.org profile

### `🌐 Deploy to WordPress.org`

Defined in `.github/workflows/deploy-wporg.yml`. It runs when a `vX.Y.Z` tag is pushed (prerelease tags containing `-` are skipped), alongside the `🚀 Release` workflow. It:

1. Checks out the tag.
2. Fails unless the tag matches the plugin header `Version`, `MD_FOR_AGENTS_VERSION`, and the `readme.txt` `Stable tag`.
3. Builds the plugin with `package-plugin.sh`, so WordPress.org receives the same files as the GitHub release ZIP.
4. Uses `10up/action-wordpress-plugin-deploy` to update `trunk/`, create `tags/X.Y.Z/`, and sync `assets/`.

To publish an existing tag (for example, the first WordPress.org release of `v1.0.7`), run the workflow manually from the Actions tab with the `tag` input, or:

```bash
gh workflow run deploy-wporg.yml -f tag=v1.0.7
```

### `🖼️ Update WordPress.org readme and assets`

Defined in `.github/workflows/wporg-assets.yml`. It runs on pushes to `main` that change `readme.txt` or the PNG files in `.wordpress-org/`, and can also be run manually. It uses `10up/action-wordpress-plugin-asset-update` to push `readme.txt` and the listing assets without releasing a new version, which is useful for updating `Tested up to` or the images.

When a release pull request bumps the `Stable tag`, push the matching tag right after merging so `tags/X.Y.Z/` exists in SVN.

The changelog is configured in `release-changelog-builder-config.json` and groups pull requests according to the repository labels, so the release notes depend on using labels consistently on PRs.
