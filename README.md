# Reusable workflows

If you want to build Docker images from your GitHub repo
automatically each time you push to the repo, this is for you!

Note that this is very opinionated.

- It requires a `Dockerfile` at the root of your GitHub repo.
- It only builds on pushes to the `main` branch.
- It builds images for linux/amd64, linux/arm64, linux/arm/v7.
- It always pushes the resulting images to GHCR.
- If the GitHub repo is named `foo/bar`, the image on GHCR will be `ghcr.io/foo/bar`.
- If your GitHub repo has a `DOCKER_HUB_TOKEN` secret, it will push the resulting images to the Docker Hub as well.
- When pushing to the Docker Hub, it pushes to `foo/bar` (aka `docker.io/foo/bar`).

Future features might include:

- [X] push to Docker Hub only when `DOCKER_HUB_TOKEN` is set
- [X] multiarch
- [ ] caching
- [ ] weekly rebuilds
- [ ] branches other than `main`

Still interested? Then here is what you need to do!

Just add the following file to your repo:

```bash
mkdir -p .github/workflows
cat > .github/workflows/automated-build.yaml <<"EOF"
name: Automated Build

on:
  push:
    branches:
      - main

jobs:
  automated-build:
    uses: jpetazzo/workflows/.github/workflows/automated-build.yaml@main
    secrets:
      DOCKER_HUB_TOKEN: ${{ secrets.DOCKER_HUB_TOKEN }}
EOF
```

If you want the images to be pushed to the Docker Hub:

1. Go get a Docker Hub token
2. Add the token to your repository (https://github.com/{user}/{repo}/settings/secrets/actions/new)
