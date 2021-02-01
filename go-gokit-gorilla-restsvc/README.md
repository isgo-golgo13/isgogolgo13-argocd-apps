## ArgoCD App Spinup Instructions ##

### Prerequisites 

**1.** 
Have active K8s cluster (k3d, ko, KIND, ...) 
**2.** 
Have active K8s cluster pre-defined namespace 'argocd'
**3.** 
Have active K8s cluster pre-deployed argocd (helm chart) install
### Deploy the ArgoCD App for 'go-gokit-gorilla-restsvc' 

```
kubectl apply -f go-gokit-gorilla-restsvc/crd.yaml
```