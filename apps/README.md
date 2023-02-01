# Deploying Hello World

Lets create the required GitOps resources for deploying <https://github.com/kameshsampath/go-hello-world>.

## Fork Application Repo

```shell
cd "$GITOPS_DEMO_HOME/work"
gh repo clone https://github.com/kameshsampath/go-hello-world
cd go-hello-world
export HELLO_WORLD_APP_FORK_REPO=$(gh repo view --json url -q '.url')
```

Let us call your <https://github.com/kameshsampath/go-hello-world> fork as `$HELLO_WORLD_APP_FORK_REPO`

## Git Repository Source

```yaml
apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: GitRepository
metadata:
  name: hello-world
  namespace: flux-system
spec:
  interval: 30s
  ref:
    branch: main
  url: $HELLO_WORLD_APP_FORK_REPO
```

Create the GitRepository which will watched for manifest changes,

```shell
cat <<EOF | kubectl apply -f -
apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: GitRepository
metadata:
  name: hello-world
  namespace: flux-system
spec:
  interval: 30s
  ref:
    branch: main
  url: $HELLO_WORLD_APP_FORK_REPO
EOF
```

## Deploy Application using GitOps

```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: hello-world
  namespace: flux-system
spec:
  interval: 5m0s
  path: ./config/serverless
  prune: true
  sourceRef:
    kind: GitRepository
    name: hello-world
  targetNamespace: default
```

Create the GitRepository which will watched for manifest changes,

```shell
cat <<EOF | kubectl apply -f -
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: hello-world
  namespace: flux-system
spec:
  interval: 5m0s
  path: ./config/serverless
  prune: true
  sourceRef:
    kind: GitRepository
    name: hello-world
  targetNamespace: default
EOF
```

After you have created these files, edit `$GITOPS_DEMO_HOME/.gitignore` and remove the entry `apps/*.yaml` to allow these files to be committed. Add, commit and push the changes to your(fork) repository.
