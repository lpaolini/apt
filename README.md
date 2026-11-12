# lpaolini APT repository

A signed Debian/APT repository on GitHub Pages for the packages of the
projects listed in [sources](sources).

```sh
sudo install -d -m 0755 /etc/apt/keyrings
curl -fsSL https://lpaolini.github.io/apt/public.key | sudo gpg --dearmor -o /etc/apt/keyrings/lpaolini.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/lpaolini.gpg] https://lpaolini.github.io/apt stable main" | sudo tee /etc/apt/sources.list.d/lpaolini.list
sudo apt update
```

Each project's latest GitHub release is in the `stable` suite and its `dev`
pre-release, if any, in the `dev` suite. To test development builds, use `dev`
instead of `stable`.

## How it is built

[publish.yaml](.github/workflows/publish.yaml) downloads the `.deb` assets of
those releases, runs [build-repository.sh](build-repository.sh) and deploys
the result to GitHub Pages. It runs on every push to `main`, and each project
runs it after releasing:

```sh
gh workflow run publish.yaml -R lpaolini/apt
```

To add a project, add its repository to `sources`, have its workflow create
GitHub releases with its `.deb` files (a release `dev` for development builds),
then run the command above. Package names must be unique across projects.

## Setup

- Secrets `APT_SIGNING_KEY` (armored secret key) and `APT_SIGNING_PASSPHRASE`.
- Settings → Pages → Source: GitHub Actions.
- In each project, a secret `APT_REPO_TOKEN`: a fine-grained token for this
  repository with Actions read and write, used to run the workflow.
