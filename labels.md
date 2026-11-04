# Labels

Elke repository van Durlinger heeft deze labels, met dezelfde naam, kleur en
omschrijving. De centrale labeler (`Durlinger/gh-workflows`, `labeler.yml`) en de
issue-formulieren gebruiken ze. Een label dat niet bestaat, maakt GitHub stil aan
in grijs en zonder omschrijving; voeg een nieuw label dus eerst hier toe en daarna
in alle repository's.

| Label | Kleur | Omschrijving | Gezet door |
|---|---|---|---|
| `bug` | `#d73a4a` | Iets werkt niet zoals bedoeld | PR-titel `fix:`, issue-formulier "Bug report" |
| `enhancement` | `#a2eeef` | Nieuwe functionaliteit of verbetering | PR-titel `feat:`, issue-formulier "Feature request" |
| `task` | `#C5DEF5` | Technische taak: refactoring, upgrades, configuratie | issue-formulier "Taak" |
| `refactor` | `#6caed5` | Herstructurering zonder gedragswijziging | PR-titel `refactor:` |
| `documentation` | `#0075ca` | Documentatie toegevoegd of verbeterd | PR-titel `docs:`, paden (`docs/`, `*.md`) |
| `dependencies` | `#0366d6` | Wijzigt afhankelijkheden (composer, npm, actions) | PR-titel `deps:`, paden (lockfiles), Dependabot |
| `frontend` | `#78da93` | Raakt de frontend (JS, CSS, views) | paden, issue-formulier (Onderdeel) |
| `backend` | `#c5bd61` | Raakt de backend (PHP: app, routes, config) | paden, issue-formulier (Onderdeel) |
| `database` | `#e73891` | Wijzigt databasemigraties | paden (`database/migrations/`) |
| `integration` | `#0052CC` | Koppeling met een externe dienst of API | paden, issue-formulier (Onderdeel "Integratie") |
| `ux` | `#e79880` | Gebruikerservaring en interactie | issue-formulier (Onderdeel "UX") |
| `blokkerend` | `#b60205` | Blokkeert gebruikers of een kritiek proces | issue-formulier (Prioriteit) |
| `hoog` | `#d93f0b` | Hoge prioriteit | issue-formulier (Prioriteit) |
| `middel` | `#fbca04` | Middelhoge prioriteit | issue-formulier (Prioriteit) |
| `laag` | `#c2e0c6` | Lage prioriteit | issue-formulier (Prioriteit) |

Padlabels (frontend, backend, database, integration, documentation,
dependencies) komen uit `.github/labeler.yml` van elke repository, omdat de
mappen per app verschillen.
