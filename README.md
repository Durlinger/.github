# .github

Standaardbestanden voor alle repository's van Durlinger. GitHub gebruikt een
bestand uit deze repository voor elke repository die zelf geen eigen versie
heeft. Deze repository moet daarvoor **publiek** zijn; zet hier dus nooit iets
geheims in.

| Bestand | Waarvoor |
|---|---|
| `SECURITY.md` | Hoe je een beveiligingslek meldt (tabblad Security van elke repository) |
| `pull_request_template.md` | Standaardopbouw van een pull request: Waarom, Wat is er veranderd, Deploy-notities |

Een repository die een eigen versie van een van deze bestanden heeft, gebruikt
die in plaats van de standaard. Wil je iets aanpassen dat voor iedereen geldt,
doe het dan hier.

## Samenhang met de rest van de baseline

- **CI/CD-workflows:** [`Durlinger/gh-workflows`](https://github.com/Durlinger/gh-workflows)
  (herbruikbare workflows via `@v1`, met de afspraken in de README daar).
- **Deploy-notities in de PR-template** worden opgepikt door de
  release-ops-notities uit gh-workflows: een regel `Deploy-Note: ...` in de
  PR-beschrijving komt op de release-PR terecht.
- **Dependabot** heeft geen centrale configuratie; elke repository heeft een
  eigen `.github/dependabot.yml`. Afspraak: een `cooldown` van minimaal 7 dagen
  per ecosysteem (de Semgrep-regel `dependabot-missing-cooldown` controleert
  dat), en voor npm `min-release-age=7` in `.npmrc`.
