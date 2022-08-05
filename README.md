# Snaplet Instant Database Preview Environment Github Action

## Usage

### With Vercel

```yaml
# .github/workflows/preview.yml

name: Preview Environment

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
        with:
          access-token: ${{ secrets.SNAPLET_ACCESS_TOKEN }}
          project-id: <YOUR_SNAPLET_PROJECT_ID>
      - uses: snaplet/vercel-action@main
        with:
          access-token: ${{ secrets.VERCEL_ACCESS_TOKEN }}
          project-id: <YOUR_VERCEL_PROJECT_ID>
        env:
          VERCEL_PREVIEW_DATABASE_URL: ${{ steps.snaplet.outputs.database-url }}
  delete:
    if: ${{ github.event.action == 'closed' }}
    runs-on: ubuntu-latest
    steps:
      - uses: snaplet/action@main
        with:
          access-token: ${{ secrets.SNAPLET_ACCESS_TOKEN }}
          delete: true
          project-id: <YOUR_SNAPLET_PROJECT_ID>
      - uses: snaplet/vercel-action@main
        with:
          access-token: ${{ secrets.VERCEL_ACCESS_TOKEN }}
          delete: true
          project-id: <YOUR_VERCEL_PROJECT_ID>
```

## Documentation

### Inputs

```yaml
access-token:
  description: Snaplet Cloud access token
  required: true
  type: string

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

project-id:
  description: Snaplet Cloud project id
  required: true
  type: string

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