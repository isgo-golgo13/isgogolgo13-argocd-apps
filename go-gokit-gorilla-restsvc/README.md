## ArgoCD App Spinup Instructions ##

### Prerequisites 

`1.` Have active K8s cluster (k3d, ko, KIND, ...) 

This creates a 3 Server Node/3 Worker/Agent Node Cluster

```
k3d cluster create go-gokit-gorilla-restsvc-cluster --api-port 127.0.0.1:6443 --k3s-server-arg "--disable=traefik" --k3s-server-arg "--disable=metrics-server" -p 80:80@loadbalancer -p 443:443@loadbalancer --agents 3 --servers 3 --verbose
```
or for a 1 Server Node/3 Worker/Agent Node Cluster

```
k3d cluster create go-gokit-gorilla-restsvc-cluster --api-port 127.0.0.1:6443 --k3s-server-arg "--disable=traefik" --k3s-server-arg "--disable=metrics-server" -p 80:80@loadbalancer -p 443:443@loadbalancer --agents 3 --servers 1 --verbose
```


`2.` Install Traefik Ingress Controller V2 Helm Chart 
```
helm repo add traefik https://containous.github.io/traefik-helm-chart
helm install traefik traefik/traefik

# Check Traefik Ingress Controller/Load Balancer is Running
kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name) 9000:9000

Go to http://localhost:9000/Dashboard/
```

`3.` Have active K8s cluster pre-defined namespace 'argocd'

```
kubectl create namespace argocd
```

`4.` Have active K8s cluster pre-deployed argocd (helm chart) install
```
helm repo update
helm install argo-cd argo/argo-cd --version 2.11.6
```

`4(a).` (Alternative) Install ArgoCD using K8s YAML `install.yaml`
```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```


`5`. Have the ArgoCD UI Dash Access 
```
In order to access the server UI you have the following options:

1. kubectl port-forward service/argo-cd-argocd-server -n default 8080:443

    and then open the browser on http://localhost:8080 and accept the certificate

2. enable ingress in the values file `service.ingress.enabled` and either
      - Add the annotation for ssl passthrough: https://github.com/argoproj/argo-cd/blob/master/docs/operator-manual/ingress.md#option-1-ssl-passthrough
      - Add the `--insecure` flag to `server.extraArgs` in the values file and terminate SSL at your ingress: https://github.com/argoproj/argo-cd/blob/master/docs/operator-manual/ingress.md#option-2-multiple-ingress-objects-and-hosts

    After reaching the UI the first time you can login with username: admin and the password will be the name of the server pod. You can get the pod name by running:

    kubectl get pods -n default -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2
```


`6.` Check the ArgoCD UI in browser at `http://localhost:8080`

### Deploy the ArgoCD App for 'go-gokit-gorilla-restsvc' 

To deploy the app ensure you are in default namespace.
```
kubens   # check if in namespace 'default'

If not do:
kubens default

Now deploy the app to ArgoCD
kubectl apply -f go-gokit-gorilla-restsvc/crd.yaml
```

