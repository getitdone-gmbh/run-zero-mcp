---
name: create-changeset
description: Decide the release impact of a change and add the matching Changeset file (package, patch/minor/major, short summary for the people who use the package or app). Every PR in the chain (kette-tokens, kette-design-enterprise, zero, zero-pages), workload.zero and run.zero repos brings one Changeset, an empty one for tooling-only work. Use before opening or updating a PR, for "changeset", "changelog entry", "release note", "does this need a release", "which version bump".
---

# Create Changeset

Changesets turn each PR into one small Markdown file in `.changeset/`. On main a bot collects them in a "Version Packages" PR; merging that PR bumps `package.json`, writes `CHANGELOG.md` and tags `v<version>`. Your job ends at the file. You never version, tag or publish.

## Steps

1. **Read the real change.** Diff against the PR base, not only the last commit: `git fetch origin && git diff origin/<base>...HEAD` plus staged, unstaged and new files (`git status`). The base is `main`, or `master` where the repo uses it.
2. **Read the repo setup.** `.changeset/config.json` and the `name` in each affected `package.json`. Take package names from the manifests, never from memory. Apps are `"private": true`; they still get versions, a changelog and a tag, just no npm publish.
3. **Pick the bump by effect on the user of the package or app**, not by file names:

   | Effect | Bump |
   | --- | --- |
   | Bug fix, visual correction, performance, security fix without new surface | `patch` |
   | New token, component, prop, endpoint, field, screen or option that is backward compatible | `minor` |
   | Removed or renamed token, component, prop, export, endpoint or field; changed default; data migration that old clients cannot handle | `major`, with migration notes |
   | CI, workflows, tests, lint, scripts, docs, skills, refactor with identical output | empty Changeset, no bump |

   A refactor that changes what ships (bundle, CSS, API response) is not tooling; judge the output. A dependency update that changes shipped code is a `patch`.
4. **Reuse before adding.** If this PR already has a Changeset for the same change, edit it. Do not touch Changesets of other PRs, generated `CHANGELOG.md` entries or `version` fields.
5. **Create the file.** Interactive: `npx changeset`. Empty: `npx changeset --empty`. These need the repo's dev dependencies (`npm ci` or `yarn`); without them use `npx -y -p @changesets/cli@3 changeset`, never a bare `npx changeset` (that fetches an unrelated package). Or write it by hand:

   ```md
   ---
   "@getitdone-gmbh/kette-tokens": minor
   ---

   Added `sizing.measure-prose` (540px) for readable paragraph width.
   ```

   An empty Changeset has frontmatter with nothing between the two `---` lines and no text. Use a short kebab-case file name that says what changed (`add-measure-prose.md`); random names from the CLI are fine too.
6. **Write the summary for the reader of the changelog.** One to three sentences: what changed for them and what they need to do. Name the affected token, component, endpoint or screen. No commit prefixes (`feat:`), no ticket noise without context, no emojis, no em dashes. Language: follow the existing `CHANGELOG.md` of the repo; when there is none, English for Kette packages and German for workload.zero and run.zero apps. In German text no comma before "und". For `major`, add a short "Migration" list with old name to new name.
7. **Check it.** `npx changeset status --since=origin/<base>` must list the intended package and bump (or nothing for an empty one). Then report the file and the decision. Commit it with the PR; do not push or open a PR unless the task says so.

## Repo notes

- **Libraries of the chain** (kette-tokens, kette-design-enterprise, zero, zero-pages): releases are bundled (two or three a day at most). Merging the Version PR tags `v<version>` and the tag starts the existing publish workflow, so the Version PR is merged only when a release is wanted.
- **workload.zero and run.zero apps**: deploys run as before. The Version PR bumps the version and writes `CHANGELOG.md`; the tag after its merge publishes nothing. Merging it is still a normal push to main, so it deploys like any other merge (APIs redeploy, the rollout manager rolls out cv-manager-gui and workload-zero-web, cv-manager-api has a login outage of 1 to 3 minutes: ask Alex first).
- **run.zero**: the public changelog on the website (`web/src/lib/changelog.ts`) is separate and written for customers. A user-visible run.zero change gets a Changeset and, when it matters to customers, an entry there too.
- **CI check**: every PR must add at least one file in `.changeset/` (the Version PR itself is exempt). A missing Changeset fails the `changeset` check; add an empty one for tooling-only work instead of bumping a package just to pass.

- **No PAT in the repo**: without the secret `CHANGESETS_TOKEN` (or `ITDONE_PUSH_TOKEN`) the workflow cannot open the Version PR. When a release is wanted: branch `changeset-release/<base>` from the base, `GITHUB_TOKEN=$(gh auth token) npm run version-packages`, commit, PR "Version Packages". Only do this when asked for a release.

## Never

- Run `changeset version`, `changeset publish`, `changeset tag`, `npm version` or `npm publish` as part of a feature PR.
- Edit `CHANGELOG.md` or `version` by hand in a feature PR.
- Put secrets, customer names or personal data in a summary.
