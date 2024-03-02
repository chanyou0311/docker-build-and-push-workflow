# docker-build-and-push-workflow

This repository contains a reusable GitHub Actions workflow for building and pushing Docker images.

## Usage

```yaml
name: release-please

on:
  push:
    branches:
      - main

permissions:
  contents: write
  pull-requests: write

jobs:
  release-please:
    runs-on: ubuntu-latest
    outputs:
      release_created: ${{ steps.release-please.outputs.release_created }}
      major: ${{ steps.release-please.outputs.major }}
      minor: ${{ steps.release-please.outputs.minor }}
      patch: ${{ steps.release-please.outputs.patch }}
    steps:
      - id: release-please
        uses: google-github-actions/release-please-action@v4

  deploy:
    needs: release-please
    if: ${{ needs.release-please.outputs.release_created }}
    uses: chanyou0311/docker-build-and-push-workflow/.github/workflows/main.yaml@v1.1.1
    with:
      IMAGE_PROJECT: sample-project
      IMAGE_NAME: sample-image
      MAJOR: ${{ needs.release-please.outputs.major }}
      MINOR: ${{ needs.release-please.outputs.minor }}
      PATCH: ${{ needs.release-please.outputs.patch }}
    secrets:
      PASSWORD: ${{ secrets.PASSWORD }}
```

## License

This workflow is released under the [MIT License](LICENSE).
