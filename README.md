# Snaplet Preview Databases Github Action

<p align="center">
  <img width="360" src="logo.png" alt="Snappy's paw makes a database appear by snapping their fingers with the title 'SNAP!'">
</p>

> Deploy Snaplet preview databases filled with production-accurate data in no time.

With this action, you can finally enjoy fully isolated and stateful preview environments. Get a new preview database filled with production-accurate data anytime you need it and plug it into your Jamstack hosting platform of choice like [Vercel](#with-vercel).

By default, the preview database will be populated with data from your latest Snaplet snapshot.

Learn more about creating snapshots in our [docs](https://docs.snaplet.dev/).

## Usage

Create a GitHub Action Workflow file in your repository following one of these examples.

### Standalone

```yaml
# .github/workflows/preview.yml

name: Preview Environment

env:
  SNAPLET_ACCESS_TOKEN: ${{ secrets.SNAPLET_ACCESS_TOKEN }}
  SNAPLET_PROJECT_ID: <YOUR_SNAPLET_PROJECT_ID>

on:
  pull_request:
    types: [opened, synchronize, closed]
    branches:
      - main

jobs:
  deploy:
    if: ${{ github.event.action == 'opened' || github.event.action == 'synchronize' }}
    runs-on: ubuntu-latest
    steps:
      - id: snaplet
        uses: snaplet/action@v1
      - run: echo ${{ steps.snaplet.outputs.database-url }}
  delete:
    if: ${{ github.event.action == 'closed' }}
    runs-on: ubuntu-latest
    steps:
      - uses: snaplet/action@v1
        with:
          delete: true
```

### With Vercel

Using [snaplet/vercel-action](https://github.com/marketplace/actions/vercel-preview-deployments)

```yaml
# .github/workflows/preview.yml

name: Preview Environment

env:
  SNAPLET_ACCESS_TOKEN: ${{ secrets.SNAPLET_ACCESS_TOKEN }}
  SNAPLET_PROJECT_ID: <YOUR_SNAPLET_PROJECT_ID>
  VERCEL_ACCESS_TOKEN: ${{ secrets.VERCEL_ACCESS_TOKEN }}
  VERCEL_PROJECT_ID: <YOUR_VERCEL_PROJECT_ID>

on:
  pull_request:
    types: [opened, synchronize, closed]
    branches:
      - main

jobs:
  deploy:
    if: ${{ github.event.action == 'opened' || github.event.action == 'synchronize' }}
    runs-on: ubuntu-latest
    steps:
      - id: snaplet
        uses: snaplet/action@v1
      - uses: snaplet/vercel-action@v1
        with:
          env: |
            DATABASE_URL=${{ steps.snaplet.outputs.database-url }}
  delete:
    if: ${{ github.event.action == 'closed' }}
    runs-on: ubuntu-latest
    steps:
      - uses: snaplet/action@v1
        with:
          delete: true
      - uses: snaplet/vercel-action@v1
        with:
          delete: true
```

## Documentation

### Environment variables

- SNAPLET_ACCESS_TOKEN
- SNAPLET_PROJECT_ID

### Inputs

```yaml
database-create-command:
  description: Command used to generate the instant database
  required: false
  type: string
  default: snaplet preview-database create --git --latest
database-delete-command:
  description: Command used to delete the instant database
  required: false
  type: string
  default: snaplet preview-database drop --git
database-url-command:
  description: Command used to get the instant database url
  required: false
  type: string
  default: snaplet preview-database url --git
delete:
  description: Delete the database
  required: false
  type: boolean
  default: false
reset:
  description: Reset the database state on each commit
  required: false
  type: boolean
  default: false
```

### Outputs

```yaml
database-url:
  description: Instant database url
```