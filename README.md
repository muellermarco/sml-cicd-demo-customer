# sml-cicd-demo-customer

AtScale SML model repo on the SML CI/CD pipeline (slug `cicd-demo-customer`).

| Event | What runs | Target |
|---|---|---|
| Pull request | `validate`: sml-cli validation + cross-reference check | none |
| Merge to `main` | `deploy-qa` | qa.atscale-se-demo.com |
| Tag `v*` | `release-live` | prod.atscale-se-demo.com, catalog `<unique_name>_main` |
| Manual (Airflow `deploy_dev__cicd-demo-customer`) | dev deploy of any branch | dev.atscale-se-demo.com, prefix `CICD-Demo-Customer` |

Put the catalog at the repo root: `catalog.yml`, `connections/`, `datasets/`,
`dimensions/`, `metrics/`, `calculations/`, `models/`. Validation needs at least
one model, so the checks stay red until the first model is committed.
