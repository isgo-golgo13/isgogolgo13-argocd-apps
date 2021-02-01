## ArgoCD App Spinup Instructions ##

### Prerequisites 

`1.` Have active K8s cluster (k3d, ko, KIND, ...) 

Done as follows:

```
Creates a 3 Server Node/3 Worker/Agent Node Cluster

k3d cluster create go-gokit-gorilla-restsvc-cluster --api-port 127.0.0.1:6443 --k3s-server-arg "--disable=traefik" --k3s-server-arg "--disable=metrics-server" -p 80:80@loadbalancer -p 443:443@loadbalancer --agents 3 --servers 3 --verbose
```

`2.` Have active K8s cluster pre-defined namespace 'argocd'

```
kubectl create namespace argocd
```

`3.` Have active K8s cluster pre-deployed argocd (helm chart) install
```
helm repo update
helm install argo-cd argo/argo-cd --version 2.11.6
```

`4`. Have the ArgoCD UI Dash Access 
```
In order to access the server UI you have the following options:

1. kubectl port-forward service/argo-cd-argocd-server -n default 8080:443

    and then open the browser on http://localhost:8080 and accept the certificate

2. enable ingress in the values file `service.ingress.enabled` and either
      - Add the annotation for ssl passthrough: https://github.com/argoproj/argo-cd/blob/master/docs/operator-manual/ingress.md#option-1-ssl-passthrough
      - Add the `--insecure` flag to `server.extraArgs` in the values file and terminate SSL at your ingress: https://github.com/argoproj/argo-cd/blob/master/docs/operator-manual/ingress.md#option-2-multiple-ingress-objects-and-hosts
```


`5.` Check the ArgoCD UI in browser at `http://localhost:8080`

### Deploy the ArgoCD App for 'go-gokit-gorilla-restsvc' 

To deploy the app ensure you are in default namespace.
```
kubens   # check if in namespace 'default'

If not do:
kubens default

Now deploy the app to ArgoCD
kubectl apply -f go-gokit-gorilla-restsvc/crd.yaml
```

