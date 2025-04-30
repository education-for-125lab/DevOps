# Allure

## 安裝
```
cd /opt
sudo curl -L -o allure-2.33.0.tgz https://github.com/allure-framework/allure2/releases/download/2.33.0/allure-2.33.0.tgz
sudo tar -xzf allure-2.33.0.tgz
sudo ln -s /opt/allure-2.33.0/bin/allure /usr/local/bin/allure

```
驗證安裝
```
allure --version

```
## 確定安裝好後 Jenkins要修改
1. 首先是docker-compose.yml 先cd到你的jenkins 的docker-compose 新增以下
```
- /opt/allure-2.33.0:/opt/allure-2.33.0  # 將宿主機上的 Allure 安裝路徑掛載到容器內
```
完整
```
services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: always
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - ~/jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock  # 允許容器訪問主機的 Docker 守護進程
      - /usr/local/bin/docker:/usr/local/bin/docker  # 確保 Docker 客戶端在容器中可用
      - /opt/allure-2.33.0:/opt/allure-2.33.0  # 將宿主機上的 Allure 安裝路徑掛載到容器內
      - /usr/lib/jvm/jdk-21.0.6-oracle-x64:/usr/lib/jvm/jdk-21.0.6-oracle-x64  # 將宿主機的 JDK 路徑掛載到容器內
    environment:
      - JAVA_HOME=/usr/lib/jvm/jdk-21.0.6-oracle-x64  # 設定 JAVA_HOME 環境變數
      - PATH=$JAVA_HOME/bin:$PATH  # 確保容器內可以使用 java 命令
      - TZ=Asia/Taipei  # 設定台灣時區
    user: root  # 這裡將 Jenkins 容器運行的用戶設置為 root，方便安裝 Docker 客戶端
    entrypoint: /bin/bash -c "apt-get update && apt-get install -y python3 python3-venv python3-pip && \
                            if ! docker ps > /dev/null; then apt-get install -y docker.io; fi && \
                            exec /usr/local/bin/jenkins.sh"


```
2. 修改後,停止現在jenkins 並配置
```
docker-compose down
docker-compose up -d
```
3. 
```
docker exec -it jenkins bash

```
```
ls /opt/allure-2.33.0
```
以上結果如下
```
qi@omvnas:~$ cd /home/primary-drive/DevOps/Jenkins
qi@omvnas:/home/primary-drive/DevOps/Jenkins$ docker-compose down
[+] Running 2/2
 ✔ Container jenkins        Removed                                                                                                                                                                                                 5.1s 
 ✔ Network jenkins_default  Removed                                                                                                                                                                                                 0.6s 
qi@omvnas:/home/primary-drive/DevOps/Jenkins$ docker-compose up -d
[+] Running 2/2
 ✔ Network jenkins_default  Created                                                                                                                                                                                                 0.3s 
 ✔ Container jenkins        Started                                                                                                                                                                                                 3.4s 
qi@omvnas:/home/primary-drive/DevOps/Jenkins$ docker exec -it jenkins bash
root@89c9441111be:/# ls /opt/allure-2.33.0
bin  config  lib  plugins
root@89c9441111be:/# 
```

4. 安裝好後回到Jenkins 將 allure加入
5. 點擊 管理Jenkins> Tools
6. 依照自己的 Allure 安裝目錄  設定
```
Allure Commandline 安裝
名稱: Allure-2.33.0
安裝目錄: /opt/allure-2.33.0
```