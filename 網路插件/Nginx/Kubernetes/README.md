1. 配置Ingress-Nginx Controller
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0/deploy/static/provider/cloud/deploy.yaml

```
2. 確認配置
```
kubectl get all -o wide -n ingress-nginx

```
3. 先輸入kubectl create namespace nginx 創建命名空間nginx再配置測試文件與nginx-svc   文件檔: nginx.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: nginx  # 添加命名空間
  labels:
    app: nginx-deploy
  name: nginx-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-deploy
  strategy: {}
  template:
    metadata:
      labels:
        app: nginx-deploy
    spec:
      containers:
      - image: nginx:latest  # 改為官方的 nginx 映像
        name: nginx
        ports:
        - containerPort: 80
        resources: {}
status: {}

---

apiVersion: v1
kind: Service
metadata:
  namespace: nginx  # 添加命名空間
  labels:
    app: nginx-deploy
  name: nginx-svc
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx-deploy
  type: LoadBalancer  # 修改為 LoadBalancer 類型
status:
  loadBalancer: {}

```
4. 配置nginx.yaml
```
kubectl apply -f nginx.yaml
```
5. 查看配置
```
kubectl get ns
kubectl get deployments -n nginx
kubectl get svc -n nginx


```