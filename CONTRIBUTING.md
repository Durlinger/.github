# Ontwikkelworkflow

Zo werken we in de repository's van Durlinger. Een repository met een eigen
`CONTRIBUTING.md` kan aanvullingen of afwijkingen hebben; die gaan dan voor.

De CI/CD-workflows zelf komen uit
[`Durlinger/gh-workflows`](https://github.com/Durlinger/gh-workflows); de
afspraken over triggers, runners en secrets staan in de README daar.

## Branches

| Branch | Doel |
|--------|------|
| `main` | Altijd releasebaar. Elke push gaat na groene tests automatisch naar staging. |
| `feature/naam`, `fix/naam` | Kortlevende werkbranch per taak |

Staging loopt altijd gelijk met de laatste `main`. Productie deployt alleen bij
een expliciete release.

## Werkwijze per taak

1. Maak een branch vanuit `main`:
   ```bash
   git switch main && git pull
   git switch -c feature/naam-van-taak
   ```
2. Open een PR naar `main`. Een **concept-PR (draft)** mag vroeg: de checks
   draaien pas zodra je hem op **Ready for review** zet. Dat scheelt wachttijd op
   de gedeelde runners.
3. De **PR-titel** volgt Conventional Commits, want hij wordt de commit op
   `main` en bepaalt de versie (zie onder). De check `Validate PR title` bewaakt
   dat.
   ```
   feat: retourformulier controleert de postcode
   fix: label wordt niet gemaakt bij een dubbele aanvraag
   ```
4. Vraagt de wijziging iets van de server dat niet automatisch gevonden wordt,
   zet dan in de PR-beschrijving een regel `Deploy-Note: ...` (zie de
   PR-template). Die komt in de operationele notities van de release.
   Nieuwe of vervallen env-variabelen, migraties en queue- of
   scheduler-wijzigingen worden wél automatisch gevonden.
5. Merge met **squash** (de enige toegestane manier). GitHub gebruikt de
   PR-titel als commit message. Verwijder de branch na de merge. Lopende checks
   van de gesloten PR worden automatisch geannuleerd.

## Van commit naar productie

```
feature/x ─► PR + squash-merge ─► main ─► tests groen? ─► deploy STAGING
                                     │
                    release-PR (release-please) ─► merge ─► GitHub Release ─► deploy PRODUCTIE
```

### Staging (automatisch, na groene tests)

Elke push naar `main` start de workflow `tests`. Slagen de tests, dan roept
`deploy-staging` de Ploi-webhook van staging aan. Falen ze, dan deployt staging
**niet**.

### Productie (bewust)

1. Controleer staging.
2. release-please houdt een release-PR bij met de bijgewerkte `CHANGELOG.md`,
   het nieuwe versienummer en een comment met de operationele notities
   (env-variabelen, migraties, Deploy-Notes). Loop die notities na.
3. Merge de release-PR. Er ontstaat een GitHub Release, en `deploy-release`
   zet `main` op de release-tag en roept de Ploi-webhook van productie aan.

### Rollback

In het Ploi-dashboard: **Activate previous release**. Ploi zet de vorige release
direct terug, zonder nieuwe deploy.

### Hotfix

**`main` bevat geen onuitgebrachte wijzigingen:** gewoon de normale weg, maar
versneld. Branch `fix/...` vanuit `main`, PR met titel `fix: ...`, squash-merge,
en merge daarna direct de release-PR.

**`main` bevat wijzigingen die nog niet live mogen:**

1. Zoek de laatste productietag en branch daarvan:
   ```bash
   git tag --sort=-version:refname | head -5
   git switch -c hotfix/kritieke-bug vX.Y.Z
   ```
2. Fix de bug, commit met `fix: ...` en push de branch.
3. Maak handmatig een GitHub Release: tag = volgende patchversie, target = de
   hotfix-branch. Publiceren start `deploy-release`.
4. Cherry-pick de fix terug naar `main` via een gewone PR, zodat hij niet
   verloren gaat.

## Versie via de PR-titel

| PR-titel begint met | Versie |
|---------------------|--------|
| `feat:` | minor (`1.2.0` → `1.3.0`) |
| `fix:` | patch (`1.2.0` → `1.2.1`) |
| `feat!:` of `BREAKING CHANGE:` in de beschrijving | major (`1.2.0` → `2.0.0`) |

Welke andere types (zoals `deps`, `perf`, `chore`) in de changelog verschijnen,
staat per repository in `release-please-config.json`. Een type dat zichtbaar is
in de changelog levert ook een patch-release op, en dus een deploy.

## Een nieuwe repository inrichten

- **Workflows:** neem de callers uit de README van
  [`gh-workflows`](https://github.com/Durlinger/gh-workflows) over. Geef secrets
  expliciet door, nooit met `secrets: inherit`.
- **Node-versie:** in `.nvmrc` (baseline `24`), gelijk aan de server.
- **npm:** `.npmrc` met `min-release-age=7`.
- **Dependabot:** `.github/dependabot.yml` met een `cooldown` van minimaal 7
  dagen per ecosysteem.
- **Ruleset op `main`:** de organisatie-ruleset regelt al: geen force push of
  verwijderen, PR verplicht, alleen squash. Voeg per repository de required
  checks toe, minimaal `security / gitleaks` en `validate / Validate PR title`.
- **Ploi:** per omgeving een site met **Quick Deploy uit** (de deploy komt uit
  GitHub Actions). Zet de Deploy-webhook-URL (Ploi → Site → Repository) als
  repository-secret:

  | Secret | Waarde |
  |--------|--------|
  | `PLOI_WEBHOOK_STAGING` | Deploy-webhook-URL van de staging-site |
  | `PLOI_WEBHOOK_PRODUCTION` | Deploy-webhook-URL van de productie-site |

  Ontbreekt een van deze secrets, dan faalt de deploy met een melding die het
  secret bij naam noemt.

## Beveiligingslekken

Meld een lek niet in een issue of PR. Zie [`SECURITY.md`](SECURITY.md).
