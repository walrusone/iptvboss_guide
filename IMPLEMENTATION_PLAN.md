# IPTVBoss Guide Implementation Plan

## Objective

Publish a searchable, mobile-friendly IPTVBoss guide from Markdown using Material for MkDocs and GitHub Pages. The first release targets the IPTVBoss 3.11.x workflow and supports both new users and power users.

## Phase 1: Deployable scaffold

- Build the guide in the existing `walrusone/iptvboss_guide` repository.
- Use `main` as the protected deployment branch.
- Add the complete navigation tree and non-empty placeholder pages.
- Add local-development instructions and a strict MkDocs build.
- Organize the supplied screenshots under `docs/assets/images/`.
- Add one complete playlist procedure with sanitized screenshots.
- Keep support tickets linked to `https://members.bosstees.net/`.

## Phase 2: First testing push

- Run `mkdocs build --strict` locally and in pull-request validation.
- Deploy only merges to `main` through GitHub Pages.
- Confirm search, navigation, responsive layout, theme switching, and image rendering.
- Allow technical testing of the deployed site before public announcement.

## Phase 3: Initial content release

Document installation, first run, playlists, EPG sources, channel mapping, layouts, output, updating, logs, diagnostics, common problems, reset/recovery, FAQ, and Basic/Pro differences.

Use task-based headings, numbered steps, exact application labels, meaningful alt text, version notes, and warnings only where they affect user safety or data.

## Phase 4: Branding and production hosting

- Confirm IPTVBoss-approved logo, colors, and terminology.
- Configure `guide.iptvboss.pro` in GitHub Pages.
- Add the exact DNS record supplied by GitHub.
- Verify HTTPS and enable enforcement.
- Configure branch protection and require validation checks.
- Obtain owner review before announcing the guide publicly.

## Phase 5: Maintenance

Keep changes in pull requests, rebuild strictly before merging, and refresh screenshots when the 3.11.x UI changes. Defer version switching, analytics, translations, comments, custom backend services, and large plugin additions until the basic guide is stable.

## Acceptance criteria

- Local and CI strict builds pass without warnings.
- The site deploys successfully from `main`.
- Navigation and search work across multiple pages.
- Screenshots render on desktop and mobile.
- No credentials, private URLs, tokens, or customer data are included.
- The custom domain and HTTPS are configured before public announcement.
- IPTVBoss owner review is complete before promotion.

