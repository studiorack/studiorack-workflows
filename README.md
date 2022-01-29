# studiorack-workflows

GitHub workflow templates for automated audio plugin builds/releases:

* Bash
* CMake 3.15.x
* GitHub workflows


## Installation

Create a .yml file in your repo at `.github/workflows/release.yml` containing:
```
name: Plugin

on:
  workflow_dispatch:
  push:
    tags:
      - "v*"

jobs:
  create_release:
    name: Create release
    runs-on: ubuntu-latest
    outputs:
      id: ${{ steps.draft_release.outputs.id }}
      upload_url: ${{ steps.draft_release.outputs.upload_url }}
    steps:
      - name: Draft release
        id: draft_release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
          draft: true

  build_release:
    name: Build release
    needs: create_release
    uses: studiorack/studiorack-workflows/.github/workflows/steinberg.yml@main
    with:
      release_id: ${{ needs.create_release.outputs.id }}
      release_url: ${{ needs.create_release.outputs.upload_url }}

  publish_release:
    name: Publish release
    needs: build_release
    runs-on: ubuntu-latest
    steps:
    - uses: eregon/publish-release@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        release_id: ${{ needs.create_release.outputs.id }}
```

Update this line to point to the template you would like to use:

    studiorack/studiorack-workflows/.github/workflows/dplug.yml@main
    studiorack/studiorack-workflows/.github/workflows/iplug.yml@main
    studiorack/studiorack-workflows/.github/workflows/juce.yml@main
    studiorack/studiorack-workflows/.github/workflows/projucer.yml@main
    studiorack/studiorack-workflows/.github/workflows/sfz.yml@main
    studiorack/studiorack-workflows/.github/workflows/steinberg.yml@main


## Usage

Release a plugin version on GitHub by simply creating a version tag:

    git tag v0.0.1
    git push && git push origin --tags

This will run an automated build and release process on GitHub Actions:

    .github/workflows/release.yml


## Contact

For more information please contact kmturley
