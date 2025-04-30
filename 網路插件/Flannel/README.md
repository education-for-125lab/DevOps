# Flannel
## 簡介
**Flannel** 是一個由 **CoreOS** 開發的虛擬網路方案，主要用於 **Kubernetes** 叢集中的 Pod 間通訊。它透過建立一個覆蓋網路（Overlay Network），讓分佈在不同節點的 Pod 能夠彼此通訊，就像它們在同一個本地網路中一樣。

---

### 🔹 Flannel 的特色簡述：

- **目的**：解決 Kubernetes 中 Pod 跨節點網路互通的問題  
- **運作原理**：透過 VXLAN、host-gw 等後端方式建立 Overlay Network  
- **架構簡單**：相對於其他 CNI（Container Network Interface）方案，Flannel 配置較為簡單  
- **支援多種後端**：包括 VXLAN、host-gw、AWS VPC、UDP 等  
- **與 etcd 整合**：早期使用 etcd 儲存網路設定（現多由 Kubernetes 自身管理）

---

### 🔸 適用情境：

Flannel 通常適用於需要一個**輕量級且易於部署的容器網路方案**，特別是在中小型 Kubernetes 叢集或初學者環境中非常常見。


## 安裝

1. 創建命名空間並添加標籤：
   ```bash
   kubectl create ns kube-flannel
   kubectl label --overwrite ns kube-flannel pod-security.kubernetes.io/enforce=privileged
   ```

   這兩個命令將會創建 `kube-flannel` 命名空間，並將其標註為 `privileged`，以確保 Flannel 安裝過程中可以執行需要特權的操作。

2. 添加 Flannel Helm 仓库：
   ```bash
   helm repo add flannel https://flannel-io.github.io/flannel/
   ```
3. 使用 Helm 安裝 Flannel：
   ```bash
   helm install flannel --set podCidr="10.42.0.0/16" --namespace kube-flannel flannel/flannel
   ```

   這個命令使用 Helm 安裝 Flannel 並設置 `podCidr`，這是 Flannel 用來分配 Pod IP 地址範圍的。設置了 "10.42.0.0/16" 這個範圍。

   - `--set podCidr="10.42.0.0/16` 是指定 Pod IP 地址範圍（CNI 設定）要跟k3s一樣。
   - `--namespace kube-flannel` 則是確保 Flannel 被安裝在你剛才創建的 `kube-flannel` 命名空間中。

4. 等待 Flannel 安裝完成：

   安裝 Flannel 可能需要一些時間。安裝完成後，可以使用以下命令查看 Flannel Pod 的狀態：
   ```bash
   kubectl get pods -n kube-flannel
   ```

   你應該會看到類似如下的輸出，表明 Flannel 的各個組件已經啟動：

   ```
    qi@QI0527-COM:/mnt/c/Users/ste92$ kubectl get pods -n kube-flannel
    NAME                    READY   STATUS    RESTARTS   AGE
    kube-flannel-ds-zf2r4   1/1     Running   0          18s
   ```

5. 輸入輸入ip a 看到zerotier的ip ztc25nn7jk   ztyouzqtg7
```
ztc25nn7jk: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 2800 qdisc fq_codel state UNKNOWN group default qlen 1000
    link/ether 6e:6e:07:6f:9e:17 brd ff:ff:ff:ff:ff:ff
    inet 10.147.20.227/24 brd 10.147.20.255 scope global ztc25nn7jk
       valid_lft forever preferred_lft forever
    inet6 fe80::6c6e:7ff:fe6f:9e17/64 scope link
       valid_lft forever preferred_lft forever
```
```
qi@QI0527-COM:/mnt/c/Users/ste92$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet 10.255.255.254/32 brd 10.255.255.254 scope global lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:e5:f1:a0 brd ff:ff:ff:ff:ff:ff
    inet 172.21.33.17/20 brd 172.21.47.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::215:5dff:fee5:f1a0/64 scope link
       valid_lft forever preferred_lft forever
72: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default
    link/ether b2:26:7c:89:13:95 brd ff:ff:ff:ff:ff:ff
    inet 10.42.0.0/32 scope global flannel.1
       valid_lft forever preferred_lft forever
    inet6 fe80::b026:7cff:fe89:1395/64 scope link
       valid_lft forever preferred_lft forever
73: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
    link/ether 3e:1b:a9:de:1b:20 brd ff:ff:ff:ff:ff:ff
    inet 10.42.0.1/24 brd 10.42.0.255 scope global cni0
       valid_lft forever preferred_lft forever
    inet6 fe80::3c1b:a9ff:fede:1b20/64 scope link
       valid_lft forever preferred_lft forever
74: vethe8808917@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default qlen 1000
    link/ether 72:37:bd:7f:fa:c0 brd ff:ff:ff:ff:ff:ff link-netns cni-54c159f0-6845-819e-f48b-a71179b7f0cd
    inet6 fe80::7037:bdff:fe7f:fac0/64 scope link
       valid_lft forever preferred_lft forever
75: veth62b130ac@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default qlen 1000
    link/ether 1e:ee:63:0b:c7:7c brd ff:ff:ff:ff:ff:ff link-netns cni-344f7058-da65-43e8-c893-dbf1bb6341f1
    inet6 fe80::1cee:63ff:fe0b:c77c/64 scope link
       valid_lft forever preferred_lft forever
76: vethc421d263@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default qlen 1000
    link/ether aa:8a:31:ae:df:f7 brd ff:ff:ff:ff:ff:ff link-netns cni-40ebfbe7-93db-004c-994d-824a83814323
    inet6 fe80::a88a:31ff:feae:dff7/64 scope link
       valid_lft forever preferred_lft forever
79: veth1a23ec74@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default qlen 1000
    link/ether 5e:00:57:07:a0:3d brd ff:ff:ff:ff:ff:ff link-netns cni-95a013c5-b052-662e-24fc-f6a2b0b6e856
    inet6 fe80::5c00:57ff:fe07:a03d/64 scope link
       valid_lft forever preferred_lft forever
80: vethb3f82590@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue master cni0 state UP group default qlen 1000
    link/ether d6:ba:07:55:25:96 brd ff:ff:ff:ff:ff:ff link-netns cni-71b98665-bd63-8841-b209-0fa1f2e93f2c
    inet6 fe80::d4ba:7ff:fe55:2596/64 scope link
       valid_lft forever preferred_lft forever
88: ztc25nn7jk: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 2800 qdisc fq_codel state UNKNOWN group default qlen 1000
    link/ether 6e:6e:07:6f:9e:17 brd ff:ff:ff:ff:ff:ff
    inet 10.147.20.227/24 brd 10.147.20.255 scope global ztc25nn7jk
       valid_lft forever preferred_lft forever
    inet6 fe80::6c6e:7ff:fe6f:9e17/64 scope link
       valid_lft forever preferred_lft forever
```
6. ls /etc/cni/net.d/ 
7. 沒資料,沒差,只接改為(若是有資料就再這行下方"isDefaultGateway": true 新增"interface": "ztc25nn7jk")
```
sudo nano /etc/cni/net.d/10-flannel.conflist
```
```
{
    "name": "flannel.1",
    "type": "flannel",
    "delegate": {
        "isDefaultGateway": true,
        "interface": "ztc25nn7jk"
    }
}

```
8. sudo systemctl restart k3s
9. kubectl rollout restart daemonset kube-flannel-ds -n kube-flannel
10. 
```
kubectl delete pod testpod --namespace=kube-flannel
kubectl run -i --tty testpod --image=busybox --restart=Never --namespace=kube-flannel -- sh

```
11. 輸入zerotier ip :  ping 10.147.20.227  
```
qi@QI0527-COM:/mnt/c/Users/ste92$ cat /etc/cni/net.d/10-flannel.conf
cat: /etc/cni/net.d/10-flannel.conf: No such file or directory
qi@QI0527-COM:/mnt/c/Users/ste92$ sudo nano /etc/cni/net.d/10-flannel.conf
qi@QI0527-COM:/mnt/c/Users/ste92$ sudo systemctl restart k3s
qi@QI0527-COM:/mnt/c/Users/ste92$ kubectl rollout restart daemonset kube-flannel-ds -n kube-flannel
daemonset.apps/kube-flannel-ds restarted
qi@QI0527-COM:/mnt/c/Users/ste92$ kubectl run -i --tty testpod --image=busybox --restart=Never --namespace=kube-flannel -- sh
If you don't see a command prompt, try pressing enter.
/ # ping 10.147.20.227  # ???? ZeroTier ??? IP ??
PING 10.147.20.227 (10.147.20.227): 56 data bytes
64 bytes from 10.147.20.227: seq=0 ttl=64 time=0.261 ms
64 bytes from 10.147.20.227: seq=1 ttl=64 time=0.070 ms
64 bytes from 10.147.20.227: seq=2 ttl=64 time=0.071 ms
64 bytes from 10.147.20.227: seq=3 ttl=64 time=0.068 ms
64 bytes from 10.147.20.227: seq=4 ttl=64 time=0.094 ms
64 bytes from 10.147.20.227: seq=5 ttl=64 time=0.065 ms
64 bytes from 10.147.20.227: seq=6 ttl=64 time=0.070 ms
64 bytes from 10.147.20.227: seq=7 ttl=64 time=0.082 ms
64 bytes from 10.147.20.227: seq=8 ttl=64 time=0.062 ms
```


## 以上 tailscale 也適用
1. ip a
```
1251: tailscale0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1280 qdisc fq_codel state UNKNOWN group default qlen 500
    link/none 
    inet 100.75.23.83/32 scope global tailscale0
       valid_lft forever preferred_lft forever
    inet6 fd7a:115c:a1e0::4a01:1755/128 scope global 
       valid_lft forever preferred_lft forever
    inet6 fe80::3f7e:a2f0:d435:56f6/64 scope link stable-privacy 
       valid_lft forever preferred_lft forever

```
2. 有資料就只接改為(若是有資料就再這行下方"isDefaultGateway": true 新增"interface": "tailscale0")
```
sudo nano /etc/cni/net.d/10-flannel.conflist
```
沒資料就
```
{
    "name": "flannel.1",
    "type": "flannel",
    "delegate": {
        "isDefaultGateway": true,
        "interface": "tailscale0"
    }
}

```
3. sudo systemctl restart k3s
4. kubectl rollout restart daemonset kube-flannel-ds -n kube-flannel
5. 重啟後就完成了
