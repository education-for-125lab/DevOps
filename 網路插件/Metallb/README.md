#  MetalLB 介紹

## MetalLB 簡介

MetalLB 是一個為 裸機 Kubernetes 叢集（Bare-metal Kubernetes）設計的 LoadBalancer 實作工具，用來讓服務能像在雲端一樣使用 `type: LoadBalancer` 的方式暴露服務。
它會將外部 IP 分配給服務，支援兩種模式：
- **Layer 2 模式**：使用 ARP/NDP 廣播方式分配 IP  
- **BGP 模式**：透過 Border Gateway Protocol 宣告路由

非常適合在沒有雲服務商（如 GCP、AWS）的本地部署環境中使用。

##  安裝Metallb

1. 安裝Metallb 用 kubectl , 先修改kube-proxy配置 
```
kubectl edit configmap -n kube-system kube-proxy
```
改成mode > mode: "ipvs"  與 strictARP:flase >strictARP:true 
2. 拉取metallb-native.yaml
```
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml
```
3. 確認是否安裝
```
kubectl get all -o wide -n metallb-system

```
依據查詢結果
```
PS C:\Users\ste92> kubectl cluster-info dump | Select-String "cluster-cidr"
PS C:\Users\ste92> kubectl cluster-info dump | Select-String "service-cluster-ip-range"

                            "--service-cluster-ip-range=10.96.0.0/12",

PS C:\Users\ste92> kubectl get nodes -o wide
NAME             STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE         KERNEL-VERSION                       CONTAINER-RUNTIME
docker-desktop   Ready    control-plane   17m   v1.30.5   192.168.65.3   <none>        Docker Desktop   5.15.167.4-microsoft-standard-WSL2   docker://27.4.0
PS C:\Users\ste92>


```

4. 創建metallb-ip-pool.yaml
```
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: default-ip-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.65.100-192.168.65.200

---

apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: default-l2
  namespace: metallb-system
spec:
  ipAddressPools:
  - default-ip-pool

```
5. 配置metallb-ip-pool.yaml
```
kubectl apply -f metallb-ip-pool.yaml
```
6. 查看配置
```
kubectl get ipaddresspools.metallb.io,l2advertisements.metallb.io -o wide -n metallb-system

```