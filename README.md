# sml-cicd-demo-customer

Demo customer repo that shows the SML CI/CD flow on an AdventureWorks Internet Sales model.

This repository holds a small AtScale semantic model written in SML (Semantic Modeling Language, the YAML format that AtScale uses to define catalogs, datasets, dimensions, and metrics as code). It is connected to the [atscale-cicd-pipeline](https://github.com/muellermarco/atscale-cicd-pipeline): every pull request is validated, every merge to `main` deploys to a QA instance, and every release tag deploys to a live instance. Use it to show how a customer team works on a semantic model with Git.

## Technologies

- AtScale 2026.9.2 (QA and live instances)
- sml-cli 2025.12.0 (pinned in the pipeline's `validate` workflow)
- SML catalog `version: 1` (see `catalog.yml`)
- Snowflake, through the AtScale connection `Snowflake Partner`
- GitHub Actions (reusable workflows from `muellermarco/atscale-cicd-pipeline`)

## Repository contents

| Path | Contents |
|---|---|
| `catalog.yml` | Catalog `cicd-demo-customer` |
| `connections/` | `con_SE_DEMO_LIBRARY_AS_ADVENTURE`: Snowflake database and schema of the AdventureWorks sample tables |
| `datasets/` | `factinternetsales` (fact), `dimproduct`, `datecustom` |
| `dimensions/` | `Dim Date` (Year, Month, Date, plus the `Time calc` calculation group), `Dim Product` (Productline, Product) |
| `metrics/` | `Sales_Amount` (Sales Amount), `Order_Quantity` (Order Quantity) |
| `calculations/` | `AVG Amount per Quantity` (Sales Amount divided by Order Quantity) |
| `models/` | `Internet Sales`, with the date dimension role-played as `Order` and `Ship` date |
| `.github/workflows/` | `validate`, `deploy-qa`, `release-live` stubs that call the pipeline |

## How the pipeline deploys this repo

| Event | Workflow | Result |
|---|---|---|
| Pull request | `validate` | sml-cli validates the catalog and a script checks the cross-references. No deploy. |
| Merge to `main` | `deploy-qa` | The catalog deploys to the QA instance. |
| Tag `v*` | `release-live` | The catalog deploys to the live instance. |

QA and live deploy the catalog as `cicd-demo-customer_main`, which is the catalog `unique_name` plus the branch name.

## Change the model and release it

1. Create a branch from `main`.
2. Edit or add the SML files in the folders listed above.
3. Validate locally: `npx -y sml-cli@2025.12.0 validate .`.
   Result: the output contains `Validation SUCCESSFUL`.
4. Open a pull request to `main`.
   Result: the `validate` check is green.
5. Merge the pull request.
   Result: `deploy-qa` runs and the QA instance shows `cicd-demo-customer_main`.
6. Test the model on the QA instance.
7. Create and push a release tag, for example `git tag v1.0 && git push origin v1.0`.
   Result: `release-live` runs and the live instance shows `cicd-demo-customer_main`.

## Set up the connection on your own instance

If you deploy this catalog to another AtScale instance, do these steps first.

1. Load the AdventureWorks tables `factinternetsales`, `dimproduct`, and `datecustom` into Snowflake.
2. Open `connections/con_SE_DEMO_LIBRARY_AS_ADVENTURE.yml`.
3. Set `as_connection` to the name of your AtScale Snowflake connection.
4. Set `database` and `schema` to the location of the tables.
