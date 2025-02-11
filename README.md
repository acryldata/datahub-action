# DataHub Ingest Action

This GitHub Action enables ingesting metadata using the DataHub CLI via your GitHub workflow

## Usage

You will need to generate a Personal Access Token from your DataHub instance and provide it to this action. It is 
recommended that that Personal Access Token is stored as a GitHub Secret. You will also need to point the action to the 
DataHub instance that is the target. 

## Inputs

| Input                 | Description                                                                                                         | Example           |
|-----------------------|---------------------------------------------------------------------------------------------------------------------|-------------------|
| datahub-server        | URL to your DataHub instance GMS                                                                                    | `https://<customer>.acryl.io/gms` |
| datahub-token         | Personal Access Token to use to connect to DataHub                                                                  |                   |
| command               | DataHub Ingestion CLI Command with all required arguments. <br/>This will be invoked as  `datahub ingest <command>` | `mcps mcp.json`    |
| optional-dependencies | Optional dependencies to install with datahub cli based on <br/>specific sources being used                         | `mysql,postgres`  |
| datahub-version       | Optional datahub version override. Defaults to the latest version                                                   | |
| python-version        | Optional python version override                                                                                    | |

## Example 

```yaml
# Example workflow .github/workflows/ingest-mcps.json

name: ingest-mcps
on:
  push:
    branches:
      - main
jobs:
  ingest-mcps:
    name: Ingest MCP json
    runs-on: ubuntu-latest
    steps:
      - name: Check out the repo
        uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: copy mcps to staging location
        id: copy-mcps
        run: |
          # Generate or copy mcps.json file to runner workspace dir
          ./generate-mcps $GITHUB_WORKSPACE/ingest.json

      - name: Ingest the file produced
        uses: acryldata/datahub-ingest-action@main
        with:
          datahub-server: ${{ vars.DATAHUB_GMS_URL }}
          datahub-token: ${{ secrets.DATAHUB_PAT }}
          command: mcps ${{ github.workspace }}/ingest.json

```

## Reference
For full list of command options that can be used, refer to [DataHub CLI Ingestion](https://datahubproject.io/docs/metadata-ingestion/cli-ingestion)
