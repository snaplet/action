# Snaplet Instant Database Github Action

Deploy an instant database per pull request. Integrate with hosting services smoothly.

## Usage

Create a GitHub Action Workflow file in your repository following one of these examples.

### With Vercel

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
      - uses: actions/checkout@v3
      - id: snaplet
        uses: snaplet/action@main
      - uses: snaplet/vercel-action@main
        with:
          env: |
            DATABASE_URL=${{ steps.snaplet.outputs.database-url }}
  delete:
    if: ${{ github.event.action == 'closed' }}
    runs-on: ubuntu-latest
    steps:
      - uses: snaplet/action@main
        with:
          delete: true
      - uses: snaplet/vercel-action@main
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
  default: snaplet database create --git --latest

database-delete-command:
  description: Command used to delete the instant database
  required: false
  type: string
  default: snaplet database delete --git

database-url-command:
  description: Command used to get the instant database url
  required: false
  type: string
  default: snaplet database url --git

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