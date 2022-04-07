# rubin-sphinx-technote-workflows

**Reusable GitHub Actions workflows for building Sphinx-based Rubin technotes.**

## ci.yaml

The [ci.yaml](.github/workflows/ci.yaml) workflow runs a Sphinx/reStructuredText technote build and uploads the HTML product to _LSST the Docs._ [This workflow is used by the reStructuredText Technote template.](https://github.com/lsst/templates/tree/main/project_templates/technote_rst)

Usage example:

```yaml
name: CI

'on': [push, pull_request]

jobs:
  call-workflow:
    uses: lsst-sqre/rubin-sphinx-technote-workflows/.github/workflows/ci.yaml@v1
    with:
      handle: sqr-065
      apt_packages: 'graphviz'
      upload: ${{ github.event_name == 'push' }}
    secrets:
      ltd_username: ${{ secrets.LTD_USERNAME }}
      ltd_password: ${{ secrets.LTD_PASSWORD }}
```

### Inputs

- `handle` (string, required). The lower-cased document handle, used as the subdomain for publishing to the `lsst.io` site.
- `apt_packages` (string, optional). The packages to install via `apt-get install` into the Ubuntu CI environment. For example, to install both `graphviz` and `imagemagick` use `apt_packages: 'graphviz imagemagick'`.
- `build_command` (string, optional). The shell command run to build the document. By default, the command is `make html` so that you can add pre- and post-build steps through the `Makefile`. However, you can explicitly change the build script through this input if required.
- `upload` (boolean, optional). A boolean flag to enable uploading the build to `lsst.io`. For example, to only upload from push events, use `upload: ${{ github.event_name == 'push' }}`. The default is to upload.

### Secrets

- `ltd_username` (required). The account username for the LTD service. Generally this is the org-wide GitHub Actions Secret, `ltd_username: ${{ secrets.LTD_USERNAME }}`. If `inputs.upload` is `false`, this can be left as an empty string.
- `ltd_password` (required). The account password for the LTD service. Generally this is the org-wide GitHub Actions Secret, `ltd_username: ${{ secrets.LTD_PASSWORD }}`. If `inputs.upload` is `false`, this can be left as an empty string.

## Developer guide

This repository enables us to centrally maintain the GitHub Actions workflows for Sphinx/reStructuredText technotes through [reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows).

Create new releases using the GitHub Releases UI and assign a tag with a semantic version, including a `v` prefix. Choose the semantic version based on compatibility for users of this workflow. If existing technotes will no longer operate under a new version of this workflow, bump the major version to `v2`.

When a release is made, a new major version tag (i.e. `v1`, `v2`) is also made or moved using [nowactions/update-majorver](https://github.com/marketplace/actions/update-major-version).
