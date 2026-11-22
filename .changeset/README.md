# Changesets

Jeder PR legt hier eine Datei an, die sagt, was sich für die Nutzer ändert. Die CI-Prüfung `changeset` lässt einen PR ohne sie durchfallen.

- `npx changeset` (nach `npm ci`) fragt nach der Stufe (patch, minor, major) und einer kurzen Zusammenfassung.
- `npx changeset --empty` für reine Tooling-PRs (CI, Skripte, Doku, Tests).

Auf main sammelt der Workflow "Changesets" die Dateien im PR "Version Packages". Dieser PR hebt die Version in `package.json` an und schreibt `CHANGELOG.md`. Nach seinem Merge setzt der Workflow den Tag `v<version>`. Deploys laufen wie bisher, der Tag veröffentlicht nichts. Ohne das Secret `CHANGESETS_TOKEN` (oder `ITDONE_PUSH_TOKEN`) kann der Workflow den PR nicht öffnen: dann `GITHUB_TOKEN=$(gh auth token) npm run version-packages` auf dem Branch `changeset-release/main` ausführen und den PR "Version Packages" von Hand öffnen. Version und `CHANGELOG.md` nie von Hand im Feature-PR ändern. Anleitung für Agenten: `.claude/skills/create-changeset/SKILL.md`.
