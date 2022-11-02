# To install the controller

`https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx`

## Details

```helm
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install [RELEASE_NAME] ingress-nginx/ingress-nginx
```
