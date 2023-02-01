# Deploying Hello World

Lets create the required GitOps resources for deploying <https://github.com/kameshsampath/go-hello-world>.

## Fork Application Repo

```shell
cd "$GITOPS_DEMO_HOME/work"
gh repo clone https://github.com/kameshsampath/go-hello-world
cd go-hello-world
export HELLO_WORLD_APP_FORK_REPO=$(gh repo view --json url -q '.url')
```

Let us call your <https://github.com/kameshsampath/go-hello-world> fork as `$HELLO_WORLD_APP_FORK_REPO`.

## Git Repository Source

Create the [GitRepository](https://fluxcd.io/flux/cmd/flux_create_source_git/) which will watched for manifest changes,

```shell
flux create source git hello-world \
  --url=$HELLO_WORLD_APP_FORK_REPO \
  --branch=main \
  --interval=30s \
  --export > ./apps/hello-world/hello-world.yaml
```

## Deploy Application using GitOps

As we will be using kustomize to deploy the application, let us create the Flux kustomization resource that will watch for manifest changes on Git repository `$HELLO_WORLD_APP_FORK_REPO`,

```shell
flux create kustomization hello-world \
  --target-namespace=default \
  --source=hello-world \
  --path="./config/serverless" \
  --prune=true \
  --interval=5m \
  --export > ./clusters/dev/hello-world-kustomization.yaml
```

After you have created these files, edit `$GITOPS_DEMO_HOME/.gitignore` and remove the entry `apps/**/*.yaml` to allow these files to be committed. Add, commit and push the changes to your(fork) repository.

Start to watch for flux to synchronize the resources,

``shell
flux get kustomizations --watch
```

A successful sync should show an output like,

```shell
```

Checkout more [guides](https://fluxcd.io/flux/guides/) and [usecases](https://fluxcd.io/flux/use-cases/) on [FluxCD](https://fluxcd.io) website.
