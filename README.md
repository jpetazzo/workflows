# Reusable GitHub Actions workflows

If you want to build Docker images from your GitHub repo
automatically each time you push to the repo, this is for you!

Note that this is very opinionated.

- One GitHub repository = one image. (It doesn't support "monorepo" layouts.)
- It requires the `Dockerfile` to be at the root of your GitHub repo.
- It only builds on pushes to the `main` branch.
- It builds a tagged image (i.e. not `latest`) on a tag push, **in additon to `latest`**
- It builds images for linux/amd64, linux/arm64, linux/arm/v7.
  (You can override this by setting the `PLATFORMS` input in your workflow.)
- It always pushes the resulting images to GHCR.
- If the GitHub repo is named `foo/bar`, the image on GHCR will be `ghcr.io/foo/bar`.
- If your GitHub repo has a `DOCKER_HUB_TOKEN` secret, it will push the resulting images to the Docker Hub as well.
- By default, it uses the Github repo owner as the Docker Hub username, but you can force it using the `DOCKER_HUB_USERNAME` input.
- When pushing to the Docker Hub, it pushes to `foo/bar` (aka `docker.io/foo/bar`).

This workflow is meant to be called on branch **or** tag push events.
If you call it on both `branches` and `tags`, `latest` will be built on both the last pushed tag and the last pushed commit 
(whichever pushed last), and it may be confusing for users.

Future features might include:

- [X] push to Docker Hub only when `DOCKER_HUB_TOKEN` is set
- [X] multiarch support
- [ ] caching
- [ ] weekly rebuilds
- [ ] branches other than `main`

Still interested? Then here is what you need to do!

First, you need to set workflow permissions accordingly
(so that the workflow can push the resulting Docker image
to GHCR). One way to do that is to go to your repository's
settings, then in `Code and automation` > `Actions` >
`General`, then in the "Workflow permissions" select
"Read and write permissions", then save.

Then, add the following file to your repo:

```bash
mkdir -p .github/workflows
cat > .github/workflows/automated-build.yaml <<"EOF"
name: Automated Build

on:
  # workflow_dispatch is optional.
  # It lets you trigger the workflow manually from GitHub's web UI.
  workflow_dispatch:
  push:
    branches:
      - main
    # Or, to build on tag pushes :
    #tags:
    #  - '*'

jobs:
  automated-build:
    uses: jpetazzo/workflows/.github/workflows/automated-build.yaml@main
    #secrets:
    #  DOCKER_HUB_TOKEN: ${{ secrets.DOCKER_HUB_TOKEN }}
    #with:
    #  DOCKER_HUB_USERNAME: myuser
    #  PLATFORMS: linux/amd64,linux/arm64
EOF
```

Commit, push, and check the "Actions" tab in your repository to see the build logs.

If you want the images to be pushed to the Docker Hub:

1. Go get a Docker Hub token
2. Add the token to your repository (https://github.com/{user}/{repo}/settings/secrets/actions/new)
3. Uncomment the `secrets:` section (and possibly the `with:` section) in your workflow file above, commit, push

## Images using this workflow

- [jpetazzo/shpod](/jpetazzo/shpod)
- [jpetazzo/color](/jpetazzo/color)
