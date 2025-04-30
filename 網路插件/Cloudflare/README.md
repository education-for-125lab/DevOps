## Cloudflare Kubernetes 配置tunnel

## 基本上用網頁版的就可以,只要記得你要用的端口有NodePort 
1. 
```
helm repo add strrl.dev https://helm.strrl.dev
helm repo update
```
2. 
apiToken : 自行獲取 accountId:登陸網頁選擇你的網域 https://dash.cloudflare.com/0fb59caa54a5c2f47df105a282ac9e9e/home/domains
```
helm upgrade --install cloudflare-tunnel-ingress-controller strrl.dev/cloudflare-tunnel-ingress-controller \
  --namespace cloudflare-tunnel-ingress-controller --create-namespace \
  --set "cloudflare.apiToken=GcqKsV5nL0BW3cB0-BZj7DTXtAwrF1Z4zalJQEb4" \
  --set "cloudflare.accountId=0fb59caa54a5c2f47df105a282ac9e9e" \
  --set "cloudflare.tunnelName=ingress-tunnel" \
  --wait


```
3. 創建命名空間nginx(看我Nginx教學已創建過)
4. 從網頁上只接用就好(不用一定用這個)
```
kubectl -n nginx create ingress nginx-via-cf-tunnel \
  --rule="nginx.totosss0527.com/*=nginx-svc:80" \
  --class=cloudflare-tunnel
```
