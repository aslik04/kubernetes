# Kubernetes: Orchestrated
Feel free to ask questions!!! It is entirely possible something in here doesn't work :)

## Docker refresher
Take a look in the `demo/docker` directory.
Start by looking in `Makefile` and replacing instances of `your-github-username-here` with your github username.

### Build and run locally
- `cd demo/docker`
- Check out waht's going on with the app!
- To build, run `make build`

### Log in to ghcr.io
We will push our image to the Github container repository, `ghcr.io`. First, we need to log in.
- Generate a Github PAT (Personal Access Token): Go to Github -> Settings -> Developer Settings -> Personal access tokens, and generate a token.
- Copy it / put it somewhere safe, then run `make login`, and paste in your token when prompted.

### Push to ghcr.io
- `make publish` Will push your image to ghcr.io, so we can use it in Kubernetes.

## Kubernetes
### Setting up
To interface with our Kubernetes cluster, we use `kubectl`, which you will need to download and set up.
To install on Linux, run:
```shell
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
# If you have sudo on your machine, do:
mv kubectl /usr/bin/kubectl
# If you don't, we can add it to PATH temporarily instead:
export PATH=$PATH:$(pwd)
```
Now we need to tell `kubectl` how to talk to our cluster. To do this:
- Create a file at `~/kubeconfig` with the following contents:
  ```yaml
  apiVersion: v1
  clusters:
  - cluster:
      insecure-skip-tls-verify: true
      server: https://mc.uwcs.co.uk:6443
    name: uwcs
  contexts:
  - context:
      cluster: uwcs
      user: uwcs
    name: uwcs
  current-context: uwcs
  kind: Config
  preferences: {}
  users:
  - name: uwcs
    user:
      client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJrRENDQVRlZ0F3SUJBZ0lJV1J0cVRxU1MrWFl3Q2dZSUtvWkl6ajBFQXdJd0l6RWhNQjhHQTFVRUF3d1kKYXpOekxXTnNhV1Z1ZEMxallVQXhOekE0T0RneU1UWTBNQjRYRFRJME1ESXlOVEUzTWpreU5Gb1hEVEkxTURJeQpOREUzTWpreU5Gb3dNREVYTUJVR0ExVUVDaE1PYzNsemRHVnRPbTFoYzNSbGNuTXhGVEFUQmdOVkJBTVRESE41CmMzUmxiVHBoWkcxcGJqQlpNQk1HQnlxR1NNNDlBZ0VHQ0NxR1NNNDlBd0VIQTBJQUJEYkp2NE02bStxaXR0SGoKVHBhQm5QT1NWRW5ieHZGQXhnUXQ5VG54ait0SUlWcHEramlGbllBbTZNTnhFTGxJZHZHdERybjU5b010YkdHdApLOHZFb1pTalNEQkdNQTRHQTFVZER3RUIvd1FFQXdJRm9EQVRCZ05WSFNVRUREQUtCZ2dyQmdFRkJRY0RBakFmCkJnTlZIU01FR0RBV2dCU05TSTdXYlcwQnJRT2p2cHB0ZWcyb01NRTYwakFLQmdncWhrak9QUVFEQWdOSEFEQkUKQWlBRk1wNFVVS2JBTndOVnZmMXBQbGFkdGtjQjJVZi9aamJ0ZWF6bUs0V1I2Z0lnUTVXcnI1Qjh1NlhmbDFubgpVWWMxZG1jbFkyejVuMEVDejBrSE93ZkR2b2c9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KLS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJlRENDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdFkyeHAKWlc1MExXTmhRREUzTURnNE9ESXhOalF3SGhjTk1qUXdNakkxTVRjeU9USTBXaGNOTXpRd01qSXlNVGN5T1RJMApXakFqTVNFd0h3WURWUVFEREJock0zTXRZMnhwWlc1MExXTmhRREUzTURnNE9ESXhOalF3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFUUkZKTlVUTDhvZWppYWlUenFMbWxaRElFNlhacGQzbWJya29odE5lM2YKbngrNXJNWWZra1ZyQmNFT0pYMnpXZWxBSkZVQnpXb2FTQUZzNzdKVi9zcTNvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVWpVaU8xbTF0QWEwRG83NmFiWG9OCnFEREJPdEl3Q2dZSUtvWkl6ajBFQXdJRFNRQXdSZ0loQUpEUEg1MHF1dEx6UWJ4bXBvMmkwZFNtd1phSmxGUkUKZEowU3VkYXh0RjA1QWlFQStURlBxYXVpcDd6alhIalFwUDVuQnE2clN3RzNuaEJTeDF1UFE5UDFjRnM9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
      client-key-data: LS0tLS1CRUdJTiBFQyBQUklWQVRFIEtFWS0tLS0tCk1IY0NBUUVFSU9MVi81R2ZpbVdlb2ZoWEppRlhHRm5ZMXp0K0tlWG44KzlhVEdzNkZWcy9vQW9HQ0NxR1NNNDkKQXdFSG9VUURRZ0FFTnNtL2d6cWI2cUsyMGVOT2xvR2M4NUpVU2R2RzhVREdCQzMxT2ZHUDYwZ2hXbXI2T0lXZApnQ2JvdzNFUXVVaDI4YTBPdWZuMmd5MXNZYTByeThTaGxBPT0KLS0tLS1FTkQgRUMgUFJJVkFURSBLRVktLS0tLQo=
  ```
- Run 
  ```shell
  export KUBECONFIG=~/kubeconfig
  ```
- You should now be able to use kubectl! Try running `kubectl get pods -A` and see what we get.

### Using kubectl to deploy our dockerised app
The syntax of kubectl is generally:
```shell
kubectl <verb> <resource type> <flags>
```
so for example, something like:
```shell
kubectl get pod --namespace kube-system
```
will list all running pods in the `kube-system` namespace.

In this repo, look under `demo/kubernetes`, you will see some Kubernetes manifests.
- Try reading through them, and think about what might happen when you apply these. Which one should you apply first, and why? 
- Modify `ns.yaml` to create a namespace with your name instead of mine. To apply this, run:
  ```shell
  kubectl apply -f ns.yaml
  ```
  Then list existing namespaces with
  ```shell
  kubectl get namespace
  ```
- Modify `deployment.yaml` to point to your image and namespace instead of mine, and apply it with `kubectl apply -f deployment.yaml`
- Likewise with `service.yaml`!
- To make your application accessible to you, run `kubectl port-forward --namespace <your namespace> service/hello 5000:5000`, then visit `127.0.0.1:5000` in your browser. Ta-da!
