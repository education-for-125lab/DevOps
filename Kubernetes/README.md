# Kubernetes

## 簡介
### ☸️ Kubernetes（K8s）簡介

Kubernetes 是一個由 Google 發起、CNCF（Cloud Native Computing Foundation）維護的開源容器編排平台，用來自動化部署、擴展、管理容器化應用程式。

它可以處理：
- 應用部署與升級
- 負載平衡與服務發現
- 自我修復（自動重啟/取代容器）
- 資源調度與擴展（水平擴充 HPA）

---

### 🚀 K8s vs K3s 比較

| 特性        | **K8s（Kubernetes）**              | **K3s（輕量版 Kubernetes）**           |
|-------------|------------------------------------|----------------------------------------|
| 開發單位    | CNCF / 社群                         | Rancher（現為 SUSE）                   |
| 適用場景    | 雲端、大型生產環境                 | IoT、邊緣運算、輕量 VM、本地測試       |
| 安裝與資源  | 較複雜、資源消耗高                  | 單一二進位，安裝快速，佔用資源低       |
| 套件支援    | 原生完整支援                       | 去除部分非必要功能，適合小型部署       |
| Etcd 資料庫 | 使用 etcd                         | 預設使用 SQLite，也可接其他 DB        |
| 二進位大小  | 多個元件（kube-apiserver, etcd 等）| 單一執行檔（<100MB）                  |


### ☸️ Kubernetes vs 🐳 Docker 差別

| 項目        | **Docker**                              | **Kubernetes**                              |
|-------------|------------------------------------------|----------------------------------------------|
| 類型        | 容器技術（Container Runtime）            | 容器編排平台（Orchestrator）                 |
| 功能        | 建立與執行容器                            | 管理大規模容器叢集、自動部署、擴展與容錯     |
| 運作層級    | 一台機器（單機）                          | 多台機器（分散式叢集）                        |
| 管理方式    | Docker CLI / Docker Compose               | `kubectl`、YAML 定義、自動排程                 |
| 使用場景    | 單一應用快速打包與執行                   | 多服務協同運作、自動擴展、高可用應用           |

🔸 簡單講：  
- Docker：幫你「**裝箱**」打包應用（建容器）  
- Kubernetes：幫你「**物流配送與倉儲管理**」（容器自動部署、管理）

> ✅ 兩者通常**一起使用**，K8s 管的是大量 Docker 容器

---

### 💰 薪水差別（依台灣職場 / 技能區分）ChatGPT 查詢

| 技能層級           | 平均月薪（約略）          | 備註 |
|--------------------|----------------------------|------|
| 🔹 熟 Docker 基本   | NT$ 45,000 – 70,000        | 初階 DevOps 或後端工程師入門門檻 |
| 🔸 熟 Docker + CI/CD | NT$ 60,000 – 90,000        | 可處理自動化流程，職缺需求高 |
| 🔺 熟 Kubernetes     | NT$ 80,000 – 150,000+      | DevOps / SRE 主力技能，加分項 |
| ⭐ DevOps 全能手     | NT$ 120,000 – 200,000+     | 有實戰、K8s 管理經驗，搭配雲端（AWS/GCP）最吃香 |

> 🚀 Kubernetes 因為門檻高、實戰少，**市場缺工又高薪**

---


### ✅ 結論

- 若你在部署企業級、雲端叢集 ➤ 使用 **K8s**
- 若你希望快速布署、學習或執行在資源有限的環境 ➤ 選擇 K3s(建議NAS 一律用K3s 除非效能真的很好選k8s)
- 想要走DevOps 盡量Docker 跟Kubernetes 都要會 , Kubernetes薪水會較多

## 前提
1. 先安裝好helm 並且要加入path
2. Metallb官網:(https://metallb.universe.tf/installation/)
3. Ingress-Nginx Controller:(https://kubernetes.github.io/ingress-nginx/deploy/)

# 安裝Helm 
1. 照官方(https://helm.sh/zh/docs/intro/install/)
Unix 版
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

# 安裝k3s
1. 更新與升級系統
``` 
sudo apt-get update && sudo apt-get upgrade -y
```
2. 安裝 curl： 安裝 curl 是用來下載 K3s 安裝腳本的工具
``` 
sudo apt-get install -y curl
```
3. 從官方網站下載並安裝 K3s
``` 
curl -sfL https://get.k3s.io | sh -
```
4.  確認 K3s 是否成功安裝並運行
```
sudo k3s kubectl get nodes
```
5.  配置 kubectl 並更改權限
```
sudo chown $(id -u):$(id -g) /etc/rancher/k3s/k3s.yaml
sudo chmod 600 /etc/rancher/k3s/k3s.yaml

```
6.  將 k3s.yaml 複製到 $HOME/.kube/config，這樣就能使用 kubectl 命令
```
mkdir -p $HOME/.kube
sudo cp /etc/rancher/k3s/k3s.yaml $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

```
7.  確認安裝好k3s 顯示 Ready 就可以了
```
kubectl get nodes
```
結果如下
```
qi@QI0527-COM:/mnt/c/Users/ste92$ sudo apt-get update && sudo apt-get upgrade -y
[sudo] password for qi:
Get:1 http://download.zerotier.com/debian/noble noble InRelease [20.5 kB]
Hit:2 http://archive.ubuntu.com/ubuntu noble InRelease
Hit:3 http://security.ubuntu.com/ubuntu noble-security InRelease
Get:4 http://archive.ubuntu.com/ubuntu noble-updates InRelease [126 kB]
Hit:5 http://archive.ubuntu.com/ubuntu noble-backports InRelease
Get:6 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 Packages [979 kB]
Get:7 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 Packages [1046 kB]
Fetched 2172 kB in 4s (590 kB/s)
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
qi@QI0527-COM:/mnt/c/Users/ste92$ sudo apt-get install -y curl
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
curl is already the newest version (8.5.0-2ubuntu10.6).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
qi@QI0527-COM:/mnt/c/Users/ste92$ curl -sfL https://get.k3s.io | sh -
[INFO]  Finding release for channel stable
[INFO]  Using v1.32.3+k3s1 as release
[INFO]  Downloading hash https://github.com/k3s-io/k3s/releases/download/v1.32.3+k3s1/sha256sum-amd64.txt
[INFO]  Downloading binary https://github.com/k3s-io/k3s/releases/download/v1.32.3+k3s1/k3s
[INFO]  Verifying binary download
[INFO]  Installing k3s to /usr/local/bin/k3s
[INFO]  Skipping installation of SELinux RPM
[INFO]  Creating /usr/local/bin/kubectl symlink to k3s
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Creating /usr/local/bin/ctr symlink to k3s
[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
[INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh
[INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env
[INFO]  systemd: Creating service file /etc/systemd/system/k3s.service
[INFO]  systemd: Enabling k3s unit
Created symlink /etc/systemd/system/multi-user.target.wants/k3s.service → /etc/systemd/system/k3s.service.
[INFO]  Host iptables-save/iptables-restore tools not found
[INFO]  Host ip6tables-save/ip6tables-restore tools not found
[INFO]  systemd: Starting k3s
qi@QI0527-COM:/mnt/c/Users/ste92$ sudo k3s kubectl get nodes
NAME         STATUS   ROLES                  AGE   VERSION
qi0527-com   Ready    control-plane,master   9s    v1.32.3+k3s1
qi@QI0527-COM:/mnt/c/Users/ste92$ kubectl get nodes
error: error loading config file "/etc/rancher/k3s/k3s.yaml": open /etc/rancher/k3s/k3s.yaml: permission denied
qi@QI0527-COM:/mnt/c/Users/ste92$ sudo chown $(id -u):$(id -g) /etc/rancher/k3s/k3s.yaml
sudo chmod 600 /etc/rancher/k3s/k3s.yaml
qi@QI0527-COM:/mnt/c/Users/ste92$ mkdir -p $HOME/.kube
sudo cp /etc/rancher/k3s/k3s.yaml $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
qi@QI0527-COM:/mnt/c/Users/ste92$ kubectl get nodes
NAME         STATUS   ROLES                  AGE   VERSION
qi0527-com   Ready    control-plane,master   35s   v1.32.3+k3s1
qi@QI0527-COM:/mnt/c/Users/ste92$
```

# 卸載 k3s
1. 停止並刪除 K3s 服務
```
sudo systemctl stop k3s

```
2. 卸載 K3s
```
sudo /usr/local/bin/k3s-uninstall.sh

```
3. 刪除 Kubernetes 配置檔案
```
sudo rm -rf /etc/rancher/k3s
rm -rf ~/.kube

```
4. 刪除多餘的檔案
```
sudo rm -rf /var/lib/rancher/k3s
sudo rm -rf /var/lib/k3s

```
5. 檢查並確認 K3s 已完全卸載
```
which k3s
which kubectl

```

# 安裝kubernetes-dashboard (kubernetes管理介面)
1. Helm 安裝kubernetes
```
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
helm repo update
helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace --namespace kubernetes-dashboard
```
2. 創建admin-user.yaml
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard

```
```
kubectl apply -f admin-user.yaml
```
3. 更改為NodePort  (可以不更改只接跳第6步,建議更改用內往穿透zerotier會需要)
```
kubectl edit svc kubernetes-dashboard-kong-proxy -n kubernetes-dashboard
```
4. 依照以下更改 type:NodePort , 增加 nodePort: 30001
```
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: Service
metadata:
  annotations:
    meta.helm.sh/release-name: kubernetes-dashboard
    meta.helm.sh/release-namespace: kubernetes-dashboard
  creationTimestamp: "2025-03-30T12:58:24Z"
  labels:
    app.kubernetes.io/instance: kubernetes-dashboard
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: kong
    app.kubernetes.io/version: "3.8"
    enable-metrics: "true"
    helm.sh/chart: kong-2.46.0
  name: kubernetes-dashboard-kong-proxy
  namespace: kubernetes-dashboard
  resourceVersion: "159257"
  uid: 0f38138d-9975-4533-afda-29efb7243388
spec:
  clusterIP: 10.43.6.181
  clusterIPs:
  - 10.43.6.181
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - name: kong-proxy-tls
    nodePort: 30001
    port: 443
    protocol: TCP
    targetPort: 8443
  selector:
    app.kubernetes.io/component: app
    app.kubernetes.io/instance: kubernetes-dashboard
    app.kubernetes.io/name: kong
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}
~
```
5. 驗證
```
kubectl get svc -n kubernetes-dashboard
```
```
qi@QI0527-COM:/mnt/d/DevOps/kubernetes-dashboard$ kubectl get svc -n kubernetes-dashboard
NAME                                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
kubernetes-dashboard-api               ClusterIP   10.43.128.31    <none>        8000/TCP        4m45s
kubernetes-dashboard-auth              ClusterIP   10.43.196.9     <none>        8000/TCP        4m45s
kubernetes-dashboard-kong-proxy        NodePort    10.43.6.181     <none>        443:30001/TCP   4m45s
kubernetes-dashboard-metrics-scraper   ClusterIP   10.43.160.249   <none>        8000/TCP        4m45s
kubernetes-dashboard-web               ClusterIP   10.43.101.120   <none>        8000/TCP        4m45s
```
6. cloudflare tunnel 指向  kubernetes-dashboard-kong-proxy
```
k3s.totosss0527.com  > https://10.43.6.181:443
```
7. 獲取token
```
kubectl -n kubernetes-dashboard create token admin-user
```

8. 設立永久token  secret.yaml
```
apiVersion: v1
kind: Secret
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/service-account.name: "admin-user"   
type: kubernetes.io/service-account-token

```
9.  設立永久token  service-account.yaml
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard

```
10. 配置
```
kubectl apply -f service-account.yaml
kubectl apply -f secret.yaml

```
11. 先登出原本登錄的輸入以下(生成出來的就是映射的用戶永久token,保存好)
```
kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d

```
12. 重新開機無法啟動,先將檔案匯出 kubectl get deployment kubernetes-dashboard-kong -n kubernetes-dashboard -o yaml > kubernetes-dashboard-kong-deployment.yaml
```
 kubectl get deployment kubernetes-dashboard-kong -n kubernetes-dashboard -o yaml > kubernetes-dashboard-kong-deployment.yaml

```
2.  再initContainers: 下與- command: 之間添加以下
      
```
- command:
        - sh
        - -c
        - rm -rf /kong_prefix/pids/* /kong_prefix/sockets/*
        image: busybox
        imagePullPolicy: Always
        name: clear-socket-files
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /kong_prefix
          name: kubernetes-dashboard-kong-prefix-dir

```
完整檔範例(不能只接複製你的檔會毀掉)
```
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "3"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{"deployment.kubernetes.io/revision":"1","meta.helm.sh/release-name":"kubernetes-dashboard","meta.helm.sh/release-namespace":"kubernetes-dashboard"},"creationTimestamp":"2025-03-30T12:58:24Z","generation":1,"labels":{"app.kubernetes.io/component":"app","app.kubernetes.io/instance":"kubernetes-dashboard","app.kubernetes.io/managed-by":"Helm","app.kubernetes.io/name":"kong","app.kubernetes.io/version":"3.8","helm.sh/chart":"kong-2.46.0"},"name":"kubernetes-dashboard-kong","namespace":"kubernetes-dashboard","resourceVersion":"170821","uid":"47c7b93f-f834-4482-9fa6-263fbfe9ff33"},"spec":{"progressDeadlineSeconds":600,"replicas":1,"revisionHistoryLimit":10,"selector":{"matchLabels":{"app.kubernetes.io/component":"app","app.kubernetes.io/instance":"kubernetes-dashboard","app.kubernetes.io/name":"kong"}},"strategy":{"rollingUpdate":{"maxSurge":"25%","maxUnavailable":"25%"},"type":"RollingUpdate"},"template":{"metadata":{"annotations":{"kuma.io/gateway":"enabled","kuma.io/service-account-token-volume":"kubernetes-dashboard-kong-token","traffic.sidecar.istio.io/includeInboundPorts":""},"creationTimestamp":null,"labels":{"app":"kubernetes-dashboard-kong","app.kubernetes.io/component":"app","app.kubernetes.io/instance":"kubernetes-dashboard","app.kubernetes.io/managed-by":"Helm","app.kubernetes.io/name":"kong","app.kubernetes.io/version":"3.8","helm.sh/chart":"kong-2.46.0","version":"3.8"}},"spec":{"automountServiceAccountToken":false,"containers":[{"env":[{"name":"KONG_ADMIN_ACCESS_LOG","value":"/dev/stdout"},{"name":"KONG_ADMIN_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_ADMIN_GUI_ACCESS_LOG","value":"/dev/stdout"},{"name":"KONG_ADMIN_GUI_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_ADMIN_LISTEN","value":"127.0.0.1:8444 http2 ssl, [::1]:8444 http2 ssl"},{"name":"KONG_CLUSTER_LISTEN","value":"off"},{"name":"KONG_DATABASE","value":"off"},{"name":"KONG_DECLARATIVE_CONFIG","value":"/kong_dbless/kong.yml"},{"name":"KONG_DNS_ORDER","value":"LAST,A,CNAME,AAAA,SRV"},{"name":"KONG_LUA_PACKAGE_PATH","value":"/opt/?.lua;/opt/?/init.lua;;"},{"name":"KONG_NGINX_WORKER_PROCESSES","value":"1"},{"name":"KONG_PLUGINS","value":"off"},{"name":"KONG_PORTAL_API_ACCESS_LOG","value":"/dev/stdout"},{"name":"KONG_PORTAL_API_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_PORT_MAPS","value":"443:8443"},{"name":"KONG_PREFIX","value":"/kong_prefix/"},{"name":"KONG_PROXY_ACCESS_LOG","value":"/dev/stdout"},{"name":"KONG_PROXY_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_PROXY_LISTEN","value":"0.0.0.0:8443 http2 ssl, [::]:8443 http2 ssl"},{"name":"KONG_PROXY_STREAM_ACCESS_LOG","value":"/dev/stdout basic"},{"name":"KONG_PROXY_STREAM_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_ROUTER_FLAVOR","value":"traditional"},{"name":"KONG_STATUS_ACCESS_LOG","value":"off"},{"name":"KONG_STATUS_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_STATUS_LISTEN","value":"0.0.0.0:8100, [::]:8100"},{"name":"KONG_STREAM_LISTEN","value":"off"},{"name":"KONG_NGINX_DAEMON","value":"off"}],"image":"kong:3.8","imagePullPolicy":"IfNotPresent","lifecycle":{"preStop":{"exec":{"command":["kong","quit","--wait=15"]}}},"livenessProbe":{"failureThreshold":3,"httpGet":{"path":"/status","port":"status","scheme":"HTTP"},"initialDelaySeconds":5,"periodSeconds":10,"successThreshold":1,"timeoutSeconds":5},"name":"proxy","ports":[{"containerPort":8443,"name":"proxy-tls","protocol":"TCP"},{"containerPort":8100,"name":"status","protocol":"TCP"}],"readinessProbe":{"failureThreshold":3,"httpGet":{"path":"/status/ready","port":"status","scheme":"HTTP"},"initialDelaySeconds":5,"periodSeconds":10,"successThreshold":1,"timeoutSeconds":5},"resources":{},"securityContext":{"allowPrivilegeEscalation":false,"capabilities":{"drop":["ALL"]},"readOnlyRootFilesystem":true,"runAsNonRoot":true,"runAsUser":1000,"seccompProfile":{"type":"RuntimeDefault"}},"terminationMessagePath":"/dev/termination-log","terminationMessagePolicy":"File","volumeMounts":[{"mountPath":"/kong_prefix/","name":"kubernetes-dashboard-kong-prefix-dir"},{"mountPath":"/tmp","name":"kubernetes-dashboard-kong-tmp"},{"mountPath":"/kong_dbless/","name":"kong-custom-dbless-config-volume"}]}],"dnsPolicy":"ClusterFirst","initContainers":[{"command":["rm","-vrf","$KONG_PREFIX/pids"],"env":[{"name":"KONG_ADMIN_ACCESS_LOG","value":"/dev/stdout"},{"name":"KONG_ADMIN_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_ADMIN_GUI_ACCESS_LOG","value":"/dev/stdout"},{"name":"KONG_ADMIN_GUI_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_ADMIN_LISTEN","value":"127.0.0.1:8444 http2 ssl, [::1]:8444 http2 ssl"},{"name":"KONG_CLUSTER_LISTEN","value":"off"},{"name":"KONG_DATABASE","value":"off"},{"name":"KONG_DECLARATIVE_CONFIG","value":"/kong_dbless/kong.yml"},{"name":"KONG_DNS_ORDER","value":"LAST,A,CNAME,AAAA,SRV"},{"name":"KONG_LUA_PACKAGE_PATH","value":"/opt/?.lua;/opt/?/init.lua;;"},{"name":"KONG_NGINX_WORKER_PROCESSES","value":"1"},{"name":"KONG_PLUGINS","value":"off"},{"name":"KONG_PORTAL_API_ACCESS_LOG","value":"/dev/stdout"},{"name":"KONG_PORTAL_API_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_PORT_MAPS","value":"443:8443"},{"name":"KONG_PREFIX","value":"/kong_prefix/"},{"name":"KONG_PROXY_ACCESS_LOG","value":"/dev/stdout"},{"name":"KONG_PROXY_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_PROXY_LISTEN","value":"0.0.0.0:8443 http2 ssl, [::]:8443 http2 ssl"},{"name":"KONG_PROXY_STREAM_ACCESS_LOG","value":"/dev/stdout basic"},{"name":"KONG_PROXY_STREAM_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_ROUTER_FLAVOR","value":"traditional"},{"name":"KONG_STATUS_ACCESS_LOG","value":"off"},{"name":"KONG_STATUS_ERROR_LOG","value":"/dev/stderr"},{"name":"KONG_STATUS_LISTEN","value":"0.0.0.0:8100, [::]:8100"},{"name":"KONG_STREAM_LISTEN","value":"off"}],"image":"kong:3.8","imagePullPolicy":"IfNotPresent","name":"clear-stale-pid","resources":{},"securityContext":{"allowPrivilegeEscalation":false,"capabilities":{"drop":["ALL"]},"readOnlyRootFilesystem":true,"runAsNonRoot":true,"runAsUser":1000,"seccompProfile":{"type":"RuntimeDefault"}},"terminationMessagePath":"/dev/termination-log","terminationMessagePolicy":"File","volumeMounts":[{"mountPath":"/kong_prefix/","name":"kubernetes-dashboard-kong-prefix-dir"},{"mountPath":"/tmp","name":"kubernetes-dashboard-kong-tmp"},{"mountPath":"/kong_dbless/","name":"kong-custom-dbless-config-volume"}]}],"restartPolicy":"Always","schedulerName":"default-scheduler","securityContext":{},"serviceAccount":"kubernetes-dashboard-kong","serviceAccountName":"kubernetes-dashboard-kong","terminationGracePeriodSeconds":30,"volumes":[{"emptyDir":{"sizeLimit":"256Mi"},"name":"kubernetes-dashboard-kong-prefix-dir"},{"emptyDir":{"sizeLimit":"1Gi"},"name":"kubernetes-dashboard-kong-tmp"},{"name":"kubernetes-dashboard-kong-token","projected":{"defaultMode":420,"sources":[{"serviceAccountToken":{"expirationSeconds":3607,"path":"token"}},{"configMap":{"items":[{"key":"ca.crt","path":"ca.crt"}],"name":"kube-root-ca.crt"}},{"downwardAPI":{"items":[{"fieldRef":{"apiVersion":"v1","fieldPath":"metadata.namespace"},"path":"namespace"}]}}]}},{"configMap":{"defaultMode":420,"name":"kong-dbless-config"},"name":"kong-custom-dbless-config-volume"}]}}},"status":{"availableReplicas":1,"conditions":[{"lastTransitionTime":"2025-03-30T12:58:24Z","lastUpdateTime":"2025-03-30T13:00:18Z","message":"ReplicaSet \"kubernetes-dashboard-kong-678c76c548\" has successfully progressed.","reason":"NewReplicaSetAvailable","status":"True","type":"Progressing"},{"lastTransitionTime":"2025-03-30T15:23:29Z","lastUpdateTime":"2025-03-30T15:23:29Z","message":"Deployment has minimum availability.","reason":"MinimumReplicasAvailable","status":"True","type":"Available"}],"observedGeneration":1,"readyReplicas":1,"replicas":1,"updatedReplicas":1}}
    meta.helm.sh/release-name: kubernetes-dashboard
    meta.helm.sh/release-namespace: kubernetes-dashboard
  creationTimestamp: "2025-03-30T12:58:24Z"
  generation: 4
  labels:
    app.kubernetes.io/component: app
    app.kubernetes.io/instance: kubernetes-dashboard
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: kong
    app.kubernetes.io/version: "3.8"
    helm.sh/chart: kong-2.46.0
  name: kubernetes-dashboard-kong
  namespace: kubernetes-dashboard
  resourceVersion: "179447"
  uid: 47c7b93f-f834-4482-9fa6-263fbfe9ff33
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app.kubernetes.io/component: app
      app.kubernetes.io/instance: kubernetes-dashboard
      app.kubernetes.io/name: kong
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      annotations:
        kubectl.kubernetes.io/restartedAt: "2025-03-31T00:14:31+08:00"
        kuma.io/gateway: enabled
        kuma.io/service-account-token-volume: kubernetes-dashboard-kong-token
        traffic.sidecar.istio.io/includeInboundPorts: ""
      creationTimestamp: null
      labels:
        app: kubernetes-dashboard-kong
        app.kubernetes.io/component: app
        app.kubernetes.io/instance: kubernetes-dashboard
        app.kubernetes.io/managed-by: Helm
        app.kubernetes.io/name: kong
        app.kubernetes.io/version: "3.8"
        helm.sh/chart: kong-2.46.0
        version: "3.8"
    spec:
      automountServiceAccountToken: false
      containers:
      - env:
        - name: KONG_ADMIN_ACCESS_LOG
          value: /dev/stdout
        - name: KONG_ADMIN_ERROR_LOG
          value: /dev/stderr
        - name: KONG_ADMIN_GUI_ACCESS_LOG
          value: /dev/stdout
        - name: KONG_ADMIN_GUI_ERROR_LOG
          value: /dev/stderr
        - name: KONG_ADMIN_LISTEN
          value: 127.0.0.1:8444 http2 ssl, [::1]:8444 http2 ssl
        - name: KONG_CLUSTER_LISTEN
          value: "off"
        - name: KONG_DATABASE
          value: "off"
        - name: KONG_DECLARATIVE_CONFIG
          value: /kong_dbless/kong.yml
        - name: KONG_DNS_ORDER
          value: LAST,A,CNAME,AAAA,SRV
        - name: KONG_LUA_PACKAGE_PATH
          value: /opt/?.lua;/opt/?/init.lua;;
        - name: KONG_NGINX_WORKER_PROCESSES
          value: "1"
        - name: KONG_PLUGINS
          value: "off"
        - name: KONG_PORTAL_API_ACCESS_LOG
          value: /dev/stdout
        - name: KONG_PORTAL_API_ERROR_LOG
          value: /dev/stderr
        - name: KONG_PORT_MAPS
          value: 443:8443
        - name: KONG_PREFIX
          value: /kong_prefix/
        - name: KONG_PROXY_ACCESS_LOG
          value: /dev/stdout
        - name: KONG_PROXY_ERROR_LOG
          value: /dev/stderr
        - name: KONG_PROXY_LISTEN
          value: 0.0.0.0:8443 http2 ssl, [::]:8443 http2 ssl
        - name: KONG_PROXY_STREAM_ACCESS_LOG
          value: /dev/stdout basic
        - name: KONG_PROXY_STREAM_ERROR_LOG
          value: /dev/stderr
        - name: KONG_ROUTER_FLAVOR
          value: traditional
        - name: KONG_STATUS_ACCESS_LOG
          value: "off"
        - name: KONG_STATUS_ERROR_LOG
          value: /dev/stderr
        - name: KONG_STATUS_LISTEN
          value: 0.0.0.0:8100, [::]:8100
        - name: KONG_STREAM_LISTEN
          value: "off"
        - name: KONG_NGINX_DAEMON
          value: "off"
        image: kong:3.8
        imagePullPolicy: IfNotPresent
        lifecycle:
          preStop:
            exec:
              command:
              - kong
              - quit
              - --wait=15
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /status
            port: status
            scheme: HTTP
          initialDelaySeconds: 5
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 5
        name: proxy
        ports:
        - containerPort: 8443
          name: proxy-tls
          protocol: TCP
        - containerPort: 8100
          name: status
          protocol: TCP
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /status/ready
            port: status
            scheme: HTTP
          initialDelaySeconds: 5
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 5
        resources: {}
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop:
            - ALL
          readOnlyRootFilesystem: true
          runAsNonRoot: true
          runAsUser: 1000
          seccompProfile:
            type: RuntimeDefault
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /kong_prefix/
          name: kubernetes-dashboard-kong-prefix-dir
        - mountPath: /tmp
          name: kubernetes-dashboard-kong-tmp
        - mountPath: /kong_dbless/
          name: kong-custom-dbless-config-volume
      dnsPolicy: ClusterFirst
      initContainers:
      - command:
        - sh
        - -c
        - rm -rf /kong_prefix/pids/* /kong_prefix/sockets/*
        image: busybox
        imagePullPolicy: Always
        name: clear-socket-files
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /kong_prefix
          name: kubernetes-dashboard-kong-prefix-dir
      - command:
        - rm
        - -vrf
        - $KONG_PREFIX/pids
        env:
        - name: KONG_ADMIN_ACCESS_LOG
          value: /dev/stdout
        - name: KONG_ADMIN_ERROR_LOG
          value: /dev/stderr
        - name: KONG_ADMIN_GUI_ACCESS_LOG
          value: /dev/stdout
        - name: KONG_ADMIN_GUI_ERROR_LOG
          value: /dev/stderr
        - name: KONG_ADMIN_LISTEN
          value: 127.0.0.1:8444 http2 ssl, [::1]:8444 http2 ssl
        - name: KONG_CLUSTER_LISTEN
          value: "off"
        - name: KONG_DATABASE
          value: "off"
        - name: KONG_DECLARATIVE_CONFIG
          value: /kong_dbless/kong.yml
        - name: KONG_DNS_ORDER
          value: LAST,A,CNAME,AAAA,SRV
        - name: KONG_LUA_PACKAGE_PATH
          value: /opt/?.lua;/opt/?/init.lua;;
        - name: KONG_NGINX_WORKER_PROCESSES
          value: "1"
        - name: KONG_PLUGINS
          value: "off"
        - name: KONG_PORTAL_API_ACCESS_LOG
          value: /dev/stdout
        - name: KONG_PORTAL_API_ERROR_LOG
          value: /dev/stderr
        - name: KONG_PORT_MAPS
          value: 443:8443
        - name: KONG_PREFIX
          value: /kong_prefix/
        - name: KONG_PROXY_ACCESS_LOG
          value: /dev/stdout
        - name: KONG_PROXY_ERROR_LOG
          value: /dev/stderr
        - name: KONG_PROXY_LISTEN
          value: 0.0.0.0:8443 http2 ssl, [::]:8443 http2 ssl
        - name: KONG_PROXY_STREAM_ACCESS_LOG
          value: /dev/stdout basic
        - name: KONG_PROXY_STREAM_ERROR_LOG
          value: /dev/stderr
        - name: KONG_ROUTER_FLAVOR
          value: traditional
        - name: KONG_STATUS_ACCESS_LOG
          value: "off"
        - name: KONG_STATUS_ERROR_LOG
          value: /dev/stderr
        - name: KONG_STATUS_LISTEN
          value: 0.0.0.0:8100, [::]:8100
        - name: KONG_STREAM_LISTEN
          value: "off"
        image: kong:3.8
        imagePullPolicy: IfNotPresent
        name: clear-stale-pid
        resources: {}
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop:
            - ALL
          readOnlyRootFilesystem: true
          runAsNonRoot: true
          runAsUser: 1000
          seccompProfile:
            type: RuntimeDefault
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /kong_prefix/
          name: kubernetes-dashboard-kong-prefix-dir
        - mountPath: /tmp
          name: kubernetes-dashboard-kong-tmp
        - mountPath: /kong_dbless/
          name: kong-custom-dbless-config-volume
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: kubernetes-dashboard-kong
      serviceAccountName: kubernetes-dashboard-kong
      terminationGracePeriodSeconds: 30
      volumes:
      - emptyDir:
          sizeLimit: 256Mi
        name: kubernetes-dashboard-kong-prefix-dir
      - emptyDir:
          sizeLimit: 1Gi
        name: kubernetes-dashboard-kong-tmp
      - name: kubernetes-dashboard-kong-token
        projected:
          defaultMode: 420
          sources:
          - serviceAccountToken:
              expirationSeconds: 3607
              path: token
          - configMap:
              items:
              - key: ca.crt
                path: ca.crt
              name: kube-root-ca.crt
          - downwardAPI:
              items:
              - fieldRef:
                  apiVersion: v1
                  fieldPath: metadata.namespace
                path: namespace
      - configMap:
          defaultMode: 420
          name: kong-dbless-config
        name: kong-custom-dbless-config-volume
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: "2025-03-30T12:58:24Z"
    lastUpdateTime: "2025-03-30T16:14:42Z"
    message: ReplicaSet "kubernetes-dashboard-kong-59968fdcc6" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  - lastTransitionTime: "2025-03-30T16:50:06Z"
    lastUpdateTime: "2025-03-30T16:50:06Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  observedGeneration: 4
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1
```
3. 修改好後應用
```
kubectl apply -f kubernetes-dashboard-kong-deployment.yaml
```
4.  驗證
```
kubectl get pods -n kubernetes-dashboard
```
```
qi@QI0527-COM:/mnt/d/DevOps/kubernetes-dashboard$ kubectl apply -f kubernetes-dashboard-kong-deployment.yaml
deployment.apps/kubernetes-dashboard-kong configured
qi@QI0527-COM:/mnt/d/DevOps/kubernetes-dashboard$ kubectl get pods -n kubernetes-dashboard
NAME                                                    READY   STATUS    RESTARTS      AGE
kubernetes-dashboard-api-7b58cd67d-5kbrm                1/1     Running   4 (41m ago)   4h1m
kubernetes-dashboard-auth-67cfb5899c-jd8f5              1/1     Running   4 (41m ago)   4h1m
kubernetes-dashboard-kong-5587c88cf4-vcj2g              0/1     Running   0             15s
kubernetes-dashboard-kong-59968fdcc6-cfqhc              1/1     Running   0             10m
kubernetes-dashboard-metrics-scraper-7d9658df44-p4zk6   1/1     Running   4 (41m ago)   4h1m
kubernetes-dashboard-web-66b75c6c7f-445rv               1/1     Running   4 (41m ago)   4h1m
qi@QI0527-COM:/mnt/d/DevOps/kubernetes-dashboard$

```




