Команды из папки **`lab_6`**
## Minikube
```bash
cd lab_6
eval "$(minikube docker-env)"
docker build -t lab6-backend:1.0.0 ./app/backend
docker build -t lab6-frontend:1.0.0 ./app/frontend
```

## Kustomize — dev
```bash
kubectl apply -k infra/k8s/kustomization/overlays/dev
kubectl apply -k app/k8s/kustomization/overlays/dev
kubectl get pods,pvc,svc -n lab6-demo
```
Проверка API
```bash
kubectl port-forward -n lab6-demo svc/lab6-backend 8080:8000
```
UI:
```bash
kubectl port-forward -n lab6-demo svc/lab6-frontend 8888:80
```

## Kustomize — prod
```bash
kubectl apply -k infra/k8s/kustomization/overlays/prod
kubectl apply -k app/k8s/kustomization/overlays/prod
```
## Helm
```bash
helm upgrade --install lab6-db ./infra/k8s/helm/postgres-infra \
  --namespace lab6-demo --create-namespace \
  -f ./infra/k8s/helm/postgres-infra/values-dev.yaml
helm upgrade --install lab6-app ./app/k8s/helm/lab6-web-app \
  --namespace lab6-demo --create-namespace \
  -f ./app/k8s/helm/lab6-web-app/values-dev.yaml
```
Отдельный namespace:

```bash
helm upgrade --install lab6-db ./infra/k8s/helm/postgres-infra \
  --namespace lab6-helm-test --create-namespace \
  -f ./infra/k8s/helm/postgres-infra/values-dev.yaml
helm upgrade --install lab6-app ./app/k8s/helm/lab6-web-app \
  --namespace lab6-helm-test --create-namespace \
  -f ./app/k8s/helm/lab6-web-app/values-dev.yaml
```
## Сборка манифестов без кластера

```bash
kubectl kustomize infra/k8s/kustomization/overlays/dev
kubectl kustomize app/k8s/kustomization/overlays/dev
helm template t ./infra/k8s/helm/postgres-infra -f ./infra/k8s/helm/postgres-infra/values-dev.yaml
helm template t ./app/k8s/helm/lab6-web-app -f ./app/k8s/helm/lab6-web-app/values-dev.yaml
```
## GHCR
```bash
kubectl set image deployment/lab6-backend backend=ghcr.io/OWNER/REPO-lab6-backend:latest -n lab6-demo
kubectl set image deployment/lab6-frontend frontend=ghcr.io/OWNER/REPO-lab6-frontend:latest -n lab6-demo
```
## Удаление dev-namespace
```bash
kubectl delete namespace lab6-demo
```
`lab6-helm-test` аналогично.
