# github-actions-demo

##

`path/to/repo/terraform/aws`

```
terraform init
terraform plan
terraform apply
```

```
terraform output -raw role
```

## マニフェストの更新

`org`と`pkgname`は環境に合わせる

```
org=virtualtech-devops
pkgname=github-actions-demo
pkgid=$(gh api \
    -H "Accept: application/vnd.github+json" \
    -H "X-GitHub-Api-Version: 2022-11-28" \
    /orgs/virtualtech-devops/packages/container/github-actions-demo/versions -q '.[0].id')
tag=$(gh api \
    -H "Accept: application/vnd.github+json" \
    -H "X-GitHub-Api-Version: 2022-11-28" \
    /orgs/$org/packages/container/$pkgname/versions/$pkgid -q '.metadata.container.tags[] | select(.!="latest")')
kubectl create deployment app --image ghcr.io/virtualtech-devops/github-actions-demo:$tag -r 1 --dry-run=client -o yaml > manifest/app.yaml
```

```
git add manifest/app.yaml
git commit -m 'update manifest' manifest/app.yaml
git push origin main
```

## マニフェストのチェック

### デプロイできるか

```
minikube -p poc start
```

```
kubectl apply -f manifest/app.yaml
```

```
kubectl get deployments.apps app -o wide
```

```
kubectl describe deployments.apps app
```
