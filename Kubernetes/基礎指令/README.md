# Kubernetes 基礎指令

# 查詢 Kubernetes 版本
```
kubectl version 
```
# 查詢所有命名空間
```
kubectl get ns
```
# 查詢所有 Pod (跨命名空間)
```
kubectl get po -A
```
# 查詢所有 Service (跨命名空間)
```
kubectl get svc -A
```
# 查詢所有 Deployment (跨命名空間)
```
kubectl get deploy -A
```
# 查詢所有 StatefulSet (跨命名空間)
```
kubectl get sts -A
```
# 查詢所有 DaemonSet (跨命名空間)
```
kubectl get ds -A
```
# 查詢所有 Ingress (跨命名空間)
```
kubectl get ing -A
```
# 查詢所有 ConfigMap (跨命名空間)
```
kubectl get cm -A
```
# 查詢所有 Secret (跨命名空間)
```
kubectl get secrets -A
```
# 查詢節點資訊
```
kubectl get nodes
```
# 查詢特定 Pod 詳細資訊
```
kubectl describe pod <POD_NAME> -n <NAMESPACE>
```
# 查詢某個 Pod 的日誌
```
kubectl logs <POD_NAME> -n <NAMESPACE>
```
# 監控 Pod 狀態 (持續更新)
```
kubectl get po -A -w
```
# 查詢 Helm 版本
```
helm version
```
# 查詢所有 Helm 安裝的套件 (跨命名空間)
```
helm ls -A
```
# 查詢特定 Helm Release 的狀態
```
helm status <RELEASE_NAME> -n <NAMESPACE>
```
# 查詢 Helm Release 的 values 配置
```
helm get values <RELEASE_NAME> -n <NAMESPACE>
```
# 查詢 Helm Release 的所有詳細資訊
```
helm get all <RELEASE_NAME> -n <NAMESPACE>
```
# 查看有安裝了哪些
```
helm repo list
```
# 刪除命名空間
```
kubectl patch namespace cert-manager -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace metallb-system -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace cattle-system -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace ingress-nginx -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace cattle-fleet-clusters-system -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace cattle-fleet-local-system -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace cattle-fleet-system -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace cattle-global-data -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace cattle-global-nt -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace cattle-impersonation-system -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace cattle-provisioning-capi-system -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace cattle-ui-plugin-system -p '{"metadata":{"finalizers":[]}}' --type=merge
kubectl patch namespace cert-manager -p '{"metadata":{"finalizers":[]}}' --type=merge


```
# 再刪不掉
```
kubectl get mutatingwebhookconfiguration,validatingwebhookconfiguration
kubectl delete mutatingwebhookconfiguration admissionregistration.k8s.io/rancher.cattle.io
kubectl delete validatingwebhookconfiguration admissionregistration.k8s.io/rancher.cattle.io

```
