# Gitlab 部署 Kubernetes教學
### 因本人架設時是用兩種電腦安裝所以有兩個網域會有不同的情況(以下安裝只要用你自己的網域就好)
### 正式架好並應用的是 (gitlab.qi0527.com)

## 安裝教學
1. 建立命名空間 kubectl create namespace gitlab (建立過就不用)
先去cloudflare 的ssl 原始伺服器申請憑證,放在cd /mnt/d/DevOps/gitlab 內
```
把憑證內容（-----BEGIN CERTIFICATE----- 和 -----END CERTIFICATE----- 之間的部分）保存為 fullchain.crt。
把私鑰內容（-----BEGIN PRIVATE KEY----- 和 -----END PRIVATE KEY----- 之間的部分）保存為 private.key。
```

2. 在cd /mnt/d/DevOps/gitlab輸入指令創建 Kubernetes Secret
```
kubectl -n gitlab create secret tls gitlab-tls \
--key /home/primary-drive/DevOps/gtilab/private.key \
--cert /home/primary-drive/DevOps/gtilab/fullchain.crt
```
from-literal=password 輸入你的smtp密碼
```
kubectl create secret generic smtp-secret \
  --from-literal=password='輸入自己的' \
  -n gitlab  
```
3. 驗證
```
kubectl get secret smtp-secret -n gitlab
kubectl -n gitlab get secret gitlab-tls

```
4. 安裝gitlab 
1. (https://artifacthub.io/packages/helm/gitlab/gitlab) ( 安裝他的檔案包)
2. 修改values.yaml
3. 看著我的修改你要的values.yaml 
4. 修改好後存取,並依照以下安裝
```
helm repo add gitlab https://charts.gitlab.io
helm repo update
```

```
helm install gitlab gitlab/gitlab \
  --namespace gitlab \
  -f values.yaml 
```

5. 建立部署gitlab的ingress (gitlab-ingress.yaml) 用cloudflare tunnel不用用這個 只接跳第7步
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: gitlab-ingress
  namespace: gitlab
  annotations:
    nginx.ingress.kubernetes.io/proxy-body-size: "0"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  ingressClassName: nginx
  rules:
    - host: gitlab.totosss0527.com  # 這裡更改為子域名
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: gitlab-webservice-default
                port:
                  number: 8181
  tls:
    - hosts:
        - gitlab.totosss0527.com  # 這裡也更改為子域名
      secretName: gitlab-tls

```
6. 部署gitlab-ingress.yaml 
```
kubectl apply -f gitlab-ingress.yaml

```
7. 驗證
```
kubectl get secret smtp-secret -n gitlab
kubectl get secret gitlab-tls -n gitlab
kubectl -n gitlab get pods 
kubectl -n gitlab get svc
kubectl -n gitlab get ingress
kubectl get pv
kubectl get pvc -n gitlab

```
若是有更改就用更新values.yaml
```
helm upgrade gitlab gitlab/gitlab --namespace gitlab -f values.yaml
```
顯示結果以下是我NAS範例
```
qi@qi0527:~$ kubectl get secret smtp-secret -n gitlab
NAME          TYPE     DATA   AGE
smtp-secret   Opaque   1      46h
qi@qi0527:~$ kubectl get secret gitlab-tls -n gitlab
NAME         TYPE                DATA   AGE
gitlab-tls   kubernetes.io/tls   2      46h
qi@qi0527:~$ kubectl -n gitlab get pods 
NAME                                               READY   STATUS      RESTARTS      AGE
gitlab-gitaly-0                                    1/1     Running     0             45h
gitlab-gitlab-exporter-6ff9647c4-m68d6             1/1     Running     0             45h
gitlab-gitlab-runner-56b8497846-v8kxg              1/1     Running     5 (45h ago)   45h
gitlab-gitlab-shell-78d55b84f5-rkvqp               1/1     Running     0             45h
gitlab-gitlab-shell-78d55b84f5-xg2sp               1/1     Running     0             45h
gitlab-kas-7f58f59484-fqwwv                        1/1     Running     3 (45h ago)   45h
gitlab-kas-7f58f59484-r2jdv                        1/1     Running     3 (45h ago)   45h
gitlab-migrations-6774a1e-fzvbj                    0/1     Completed   0             45h
gitlab-minio-7c9dcd689c-mvkpb                      1/1     Running     0             45h
gitlab-minio-create-buckets-17b12c4-tdhxj          0/1     Completed   0             45h
gitlab-nginx-ingress-controller-75c8d9c8fb-8wgcj   1/1     Running     0             45h
gitlab-nginx-ingress-controller-75c8d9c8fb-g6tdr   1/1     Running     0             45h
gitlab-postgresql-0                                2/2     Running     0             45h
gitlab-prometheus-server-779547674b-x2n89          2/2     Running     0             45h
gitlab-redis-master-0                              2/2     Running     0             45h
gitlab-registry-bbc57c5d4-22w78                    1/1     Running     1 (45h ago)   45h
gitlab-registry-bbc57c5d4-2dkw4                    1/1     Running     1 (45h ago)   45h
gitlab-sidekiq-all-in-1-v2-7978b45758-chpgc        1/1     Running     0             45h
gitlab-toolbox-7f749f8dd-xlrzq                     1/1     Running     0             45h
gitlab-webservice-default-56f4b64678-56d8v         2/2     Running     0             45h
gitlab-webservice-default-56f4b64678-whjpq         2/2     Running     0             45h
qi@qi0527:~$ kubectl -n gitlab get svc
NAME                                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                        AGE
gitlab-gitaly                             ClusterIP   None            <none>        8075/TCP,9236/TCP                              45h
gitlab-gitlab-exporter                    ClusterIP   10.43.171.46    <none>        9168/TCP                                       45h
gitlab-gitlab-shell                       ClusterIP   10.43.136.109   <none>        22/TCP                                         45h
gitlab-kas                                ClusterIP   10.43.156.5     <none>        8150/TCP,8153/TCP,8154/TCP,8151/TCP            45h
gitlab-minio-svc                          ClusterIP   10.43.64.251    <none>        9000/TCP                                       45h
gitlab-nginx-ingress-controller           NodePort    10.43.90.223    <none>        80:31200/TCP,443:31520/TCP,22:32222/TCP        45h
gitlab-nginx-ingress-controller-metrics   ClusterIP   10.43.115.159   <none>        10254/TCP                                      45h
gitlab-postgresql                         ClusterIP   10.43.10.211    <none>        5432/TCP                                       45h
gitlab-postgresql-hl                      ClusterIP   None            <none>        5432/TCP                                       45h
gitlab-postgresql-metrics                 ClusterIP   10.43.221.225   <none>        9187/TCP                                       45h
gitlab-prometheus-server                  ClusterIP   10.43.1.17      <none>        80/TCP                                         45h
gitlab-redis-headless                     ClusterIP   None            <none>        6379/TCP                                       45h
gitlab-redis-master                       ClusterIP   10.43.88.155    <none>        6379/TCP                                       45h
gitlab-redis-metrics                      ClusterIP   10.43.87.58     <none>        9121/TCP                                       45h
gitlab-registry                           ClusterIP   10.43.217.111   <none>        5000/TCP                                       45h
gitlab-webservice-default                 NodePort    10.43.132.172   <none>        8080:30080/TCP,8181:30081/TCP,8083:30083/TCP   45h
qi@qi0527:~$ kubectl -n gitlab get ingress
NAME                        CLASS          HOSTS                 ADDRESS        PORTS     AGE
gitlab-kas                  gitlab-nginx   kas.qi0527.com        10.43.90.223   80, 443   45h
gitlab-minio                gitlab-nginx   minio.qi0527.com      10.43.90.223   80, 443   45h
gitlab-registry             gitlab-nginx   registry.qi0527.com   10.43.90.223   80, 443   45h
gitlab-webservice-default   gitlab-nginx   gitlab.qi0527.com     10.43.90.223   80, 443   45h
qi@qi0527:~$ kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
pvc-5510e6d8-2580-42e4-a694-0c50f1bccc9f   8Gi        RWO            Delete           Bound    gitlab/gitlab-prometheus-server           local-path     <unset>                          45h
pvc-56432a97-d1d8-41df-b9a1-27b8d40d28f8   50Gi       RWO            Delete           Bound    gitlab/repo-data-gitlab-gitaly-0          local-path     <unset>                          45h
pvc-76429e11-9ece-4db6-87c2-0d2efc6a488e   100Gi      RWO            Delete           Bound    gitlab/data-gitlab-postgresql-0           local-path     <unset>                          45h
pvc-e130ddbd-551f-4831-8f54-3ebfeb35bf00   10Gi       RWO            Delete           Bound    gitlab/gitlab-minio                       local-path     <unset>                          45h
pvc-e51ac6d8-d523-4e44-a56f-d218a8b3faf8   8Gi        RWO            Delete           Bound    gitlab/redis-data-gitlab-redis-master-0   local-path     <unset>                          45h
qi@qi0527:~$ kubectl get pvc -n gitlab


```
8. 更改NodePort  (type: NodePort 與nodePort: 30080 nodePort: 30081 nodePort: 30083 )
```
kubectl edit svc gitlab-webservice-default -n gitlab

```
9. 更改NodePort  (type: NodePort 與 ssh 部份 改成你要的 nodePort: 32222 )
```
kubectl edit svc gitlab-nginx-ingress-controller  -n gitlab
```

10. cloudflare tunnel連線 (若是已經有在docker上安裝可以將tunnel 指向 內網穿透ip 例:100.43.243.43:30081 這樣可以跳到13步驟)
```
helm repo add strrl.dev https://helm.strrl.dev
helm repo update
```
```

helm upgrade --install --wait \
  -n cloudflare-tunnel-ingress-controller --create-namespace \
  cloudflare-tunnel-ingress-controller \
  strrl.dev/cloudflare-tunnel-ingress-controller \
  --set=cloudflare.apiToken="輸入自己的",cloudflare.accountId="輸入自己的",cloudflare.tunnelName="ingress-tunnel" 


```
```
qi@QI0527-COM:/mnt/c/Users/ste92$ helm repo add strrl.dev https://helm.strrl.dev
helm repo update
"strrl.dev" already exists with the same configuration, skipping
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "kubernetes-dashboard" chart repository
...Successfully got an update from the "jetstack" chart repository
...Successfully got an update from the "cloudflare" chart repository
...Successfully got an update from the "strrl.dev" chart repository
...Successfully got an update from the "rancher-stable" chart repository
...Successfully got an update from the "jenkins" chart repository
...Successfully got an update from the "rancher-latest" chart repository
...Successfully got an update from the "gitlab" chart repository
Update Complete. ⎈Happy Helming!⎈
qi@QI0527-COM:/mnt/c/Users/ste92$ helm upgrade --install --wait \
  -n cloudflare-tunnel-ingress-controller --create-namespace \
  cloudflare-tunnel-ingress-controller \
  strrl.dev/cloudflare-tunnel-ingress-controller \
  --set=cloudflare.apiToken="輸入自己的" \
  --set=cloudflare.accountId="輸入自己的" \
  --set=cloudflare.tunnelName="ingress-tunnel"
Release "cloudflare-tunnel-ingress-controller" does not exist. Installing it now.
NAME: cloudflare-tunnel-ingress-controller
LAST DEPLOYED: Tue Apr  1 13:52:37 2025
NAMESPACE: cloudflare-tunnel-ingress-controller
STATUS: deployed
REVISION: 1
TEST SUITE: None
qi@QI0527-COM:/mnt/c/Users/ste92$ kubectl get pods -n cloudflare-tunnel-ingress-controller
NAME                                                    READY   STATUS    RESTARTS   AGE
cloudflare-tunnel-ingress-controller-55576c787f-xprg4   1/1     Running   0          9s
qi@QI0527-COM:/mnt/c/Users/ste92$
```
11. cloudflare連線gitlab ,可以網頁設定
```
kubectl -n gitlab create ingress gitlab-via-cf-tunnel \
  --rule="gitlab.totosss0527.com/*=gitlab-webservice-default:8181" \
  --class=cloudflare-tunnel

```
```
kubectl get pods -n cloudflare-tunnel-ingress-controller

kubectl get svc -n cloudflare-tunnel-ingress-controller

```
12. 驗證 
```
kubectl -n gitlab get ingress
```
13. 網頁輸入(https://gitlab.totosss0527.com/)登錄後記得改密碼,初始 帳號:root 密碼:token (輸入以下指令)
```
kubectl -n gitlab get secret gitlab-gitlab-initial-root-password -ojsonpath='{.data.password}' | base64 --decode ; echo
```

## gitlab ssh 設定
1. cd C:\Users\ste92\.ssh
2. dir 如果該資料夾中有 id_rsa 和 id_rsa.pub，則代表已有金鑰。如果沒有，則需要創建新的 SSH 金鑰。
3. ssh-keygen -t rsa -b 4096 -C "ste920527@gmail.com"   輸入自己的GMAIL
3. 我是都按Enter
```
Enter file in which to save the key：這個步驟是問你要將 SSH 金鑰保存在什麼位置。預設會保存到 C:\Users\ste92\.ssh\id_rsa，你可以直接按 Enter，接受預設選項，或者指定不同的路徑來保存金鑰。

如果你想保留預設位置，直接按 Enter 即可。
Enter passphrase (empty for no passphrase)：這是設定一個密碼來保護你的私鑰。你可以選擇設置密碼來增加安全性，或者如果不想設定密碼，就直接按 Enter 跳過。

Enter same passphrase again：這是再次輸入密碼來確認是否正確。如果你選擇不設置密碼，則直接按 Enter。

當你完成這些步驟後，SSH 金鑰對（私鑰和公鑰）會被生成並保存在 C:\Users\ste92\.ssh 目錄中。私鑰檔案是 id_rsa，公鑰檔案是 id_rsa.pub。
```
4. 打開
```
notepad C:\Users\ste92\.ssh\id_rsa.pub

```
5. 
```
登入你的 GitLab 帳號。
點擊右上角的用戶頭像，選擇 Settings（設定）。
在左側菜單中選擇 SSH Keys。
在 "Key" 欄位中粘貼你剛剛複製的公鑰內容。
在 "Title" 欄位中給你的金鑰命名，例如 My Laptop SSH Key。
點擊 Add key。
```
6. 測試

zerotier  內網穿透
```
ssh -T -p 32222 git@10.243.35.243 

```
tailscale 內網穿透
```
ssh -T -p 32222 git@100.75.23.83

```
7. 看到Welcome to GitLab, @qi_0527! 就成功了

```
Microsoft Windows [版本 10.0.26100.3775]
(c) Microsoft Corporation. 著作權所有，並保留一切權利。

C:\Users\ste92>cd C:\Users\ste92\OneDrive\桌面\SSH-Gitlab-Clone

C:\Users\ste92\OneDrive\桌面\SSH-Gitlab-Clone>ssh -T -p 32222 git@100.75.23.83
Welcome to GitLab, @qi_0527!

C:\Users\ste92\OneDrive\桌面\SSH-Gitlab-Clone>ssh -T -p 32222 git@10.243.35.243
Welcome to GitLab, @qi_0527!

C:\Users\ste92\OneDrive\桌面\SSH-Gitlab-Clone>git clone ssh://git@100.75.23.83:32222/qi_0527/devops-test.git
Cloning into 'devops-test'...
remote: Enumerating objects: 42, done.
remote: Counting objects: 100% (42/42), done.
remote: Compressing objects: 100% (37/37), done.
remote: Total 42 (delta 10), reused 0 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (42/42), 10.55 KiB | 3.52 MiB/s, done.
Resolving deltas: 100% (10/10), done.

C:\Users\ste92\OneDrive\桌面\SSH-Gitlab-Clone>dir
 磁碟區 C 中的磁碟沒有標籤。
 磁碟區序號:  D674-6CF6

 C:\Users\ste92\OneDrive\桌面\SSH-Gitlab-Clone 的目錄

2025/04/20  上午 02:13    <DIR>          .
2025/04/20  上午 02:10    <DIR>          ..
2025/04/20  上午 02:13    <DIR>          devops-test
               0 個檔案               0 位元組
               3 個目錄  809,660,563,456 位元組可用

C:\Users\ste92\OneDrive\桌面\SSH-Gitlab-Clone>cd devops-test

C:\Users\ste92\OneDrive\桌面\SSH-Gitlab-Clone\devops-test>dir
 磁碟區 C 中的磁碟沒有標籤。
 磁碟區序號:  D674-6CF6

 C:\Users\ste92\OneDrive\桌面\SSH-Gitlab-Clone\devops-test 的目錄

2025/04/20  上午 02:13    <DIR>          .
2025/04/20  上午 02:13    <DIR>          ..
2025/04/20  上午 02:13    <DIR>          .idea
2025/04/20  上午 02:13               211 app.py
2025/04/20  上午 02:13               342 Dockerfile
2025/04/20  上午 02:13             5,736 Jenkinsfile
2025/04/20  上午 02:13                30 requirements.txt
2025/04/20  上午 02:13    <DIR>          templates
2025/04/20  上午 02:13    <DIR>          test
2025/04/20  上午 02:13    <DIR>          __pycache__
               4 個檔案           6,319 位元組
               6 個目錄  809,660,563,456 位元組可用

C:\Users\ste92\OneDrive\桌面\SSH-Gitlab-Clone\devops-test>
```
8. 整題如下
```
Microsoft Windows [版本 10.0.26100.3476]
(c) Microsoft Corporation. 著作權所有，並保留一切權利。

C:\Users\ste92>cd C:\Users\ste92\.ssh

C:\Users\ste92\.ssh>dir
 磁碟區 C 中的磁碟沒有標籤。
 磁碟區序號:  98EA-8C21

 C:\Users\ste92\.ssh 的目錄

2025/01/24  下午 11:19    <DIR>          .
2025/03/17  下午 11:29    <DIR>          ..
2025/01/24  下午 11:19               840 known_hosts
2025/01/24  下午 11:19                96 known_hosts.old
               2 個檔案             936 位元組
               2 個目錄  757,145,763,840 位元組可用

C:\Users\ste92\.ssh>ssh-keygen -t rsa -b 4096 -C "ste920527@gmail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\ste92/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\ste92/.ssh/id_rsa
Your public key has been saved in C:\Users\ste92/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:/bkp2jnY4OvEyCR37oNVrdHXv0ssHQlrmTxUF5RBUkU ste920527@gmail.com
The key's randomart image is:
+---[RSA 4096]----+
|             .+OE|
|              o..|
|           o o . |
|         .o = * o|
|    . o S..o O o.|
|     = =o ....+ o|
|      o+++  o. +.|
|      .o+.+. oo. |
|       .=+ooo  ..|
+----[SHA256]-----+

C:\Users\ste92\.ssh>notepad C:\Users\ste92\.ssh\id_rsa.pub

C:\Users\ste92\.ssh>
```
## 卸載部署 gitlab
```
helm uninstall -n gitlab gitlab

```
"C:\Users\ste92\.ssh\config"
```
Host gitlab.ssh.totosss0527.com
  User qi_0527
  Hostname gitlab.ssh.totosss0527.com  # 這是 Cloudflare Tunnel 的域名
  IdentityFile C:\Users\ste92\.ssh\id_rsa  # 你的私鑰路徑
  Port 22  # 默認的 SSH 端口

```


## 問題
1. 若是因分享器有改動ip 例如:192.168.0.227改192.168.10.183 請記得刪除原本gitlab-tls 並重新提交 (會影響到webhook)
```
kubectl -n gitlab delete secret gitlab-tls
```
```
cd /home/primary-drive/DevOps/gtilab
```
```
kubectl -n gitlab create secret tls gitlab-tls \
--key /home/primary-drive/DevOps/gtilab/private.key \
--cert /home/primary-drive/DevOps/gtilab/fullchain.crt
```
## 參考教學

1. Helm gitlab 教學(https://blog.csdn.net/networken/article/details/132113051?spm=1001.2014.3001.5506)
2. Helm gitlab 教學 (https://blog.csdn.net/weixin_44252186/article/details/143878637?spm=1001.2014.3001.5501)


