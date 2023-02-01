# Hello World!, the GitOps way

A demo to show case how to deploy the traditional Hello World application using [GitOps](https://www.gitops.tech/). The goal of this demo is to get started with GitOps on your laptops, understand its principles etc.,

For this demo we will be using [FluxCD](https://fluxcd.io/) as the GitOps platform on Kubernetes.

## Tools

- [Task](https://taskfile.dev/)
- [Flux](https://fluxcd.io/flux/cmd/)
- [K3D](https://k3d.io)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [GitHub CLI](https://github.com/cli/cli)

## Whats  required

- [GitHub PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
  
- A GitHub user to fork the GitOps Demo Fork

## Fork the demo repo

Run the following command to fork the demo repo under your account,

```shell
gh repo fork <https://github.com/kameshsampath/flux-hello-world>
cd flux-hello-world
```

## Bootstrap

```shell
flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=flux-hello-world \
  --branch=main \
  --personal \
  --path=./clusters/dev
```

### GitOps UI

For this demo we will be using [Weave GitOps OSS](https://github.com/weaveworks/weave-gitops) for UI and Dashboard.

Check if the Weave GitOps OSS dashboard is up,

```shell
```

Once its running its accessible via <http://127.0.0.1.sslip.io:30091>.

Ensure all the required infrastructure components are up and running,

### Knative

```shell
kubectl get pods -n knative serving
```

#### Knative Ingress Check

```shell
kubectl describe configmap/config-network --namespace knative-serving
```

#### Knative Domain Check

```shell
kubectl describe configmap/config-domain --namespace knative-serving
```

#### Deploy test app

```shell
cat <<EOF | kubectl apply -f -
apiVersion: serving.knative.dev/v1 # Current version of Knative
kind: Service
metadata:
  name: helloworld-go # The name of the app
  namespace: default # The namespace the app will use
spec:
  template:
    spec:
      containers:
        - image: gcr.io/knative-samples/helloworld-go # The URL to the image of the app
          env:
            - name: TARGET # The environment variable printed out by the sample app
              value: "Hello Knative Serving is up and running with Kourier!!"
EOF
```

Wait for the service to be ready,

```shell
watch kubectl get ksvc
```

If all went well you can cURL the service <http://helloworld-go.default.127.0.0.1.sslip.io:30080/>

Clean up the test service using the command,

```shell
kubectl delete kgsvc helloworld-go
```

## Deploy Hello World Application

Check the [README.md](./apps/README.md) on how to deploy the Hello World Application using GitOps.

