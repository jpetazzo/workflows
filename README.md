# Reusable workflows

To get automated builds on your GitHub repo, create the following file:

```bash
mkdir -p .github/workflows
cat > .github/workflows/automated-buid.yaml <<"EOF"
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

If the repo is e.g. `jpetazzo/hello`, it will automatically build+push to
`jpetazzo/hello` on the Docker Hub and on `ghcr.io/jpetazzo/hello` on GitHub
Container Registry.

TODO:
- make DOCKER_HUB_TOKEN optional
- support multiarch
- support caching
- do weekly rebuilds
- support other branches/tags than `main`
