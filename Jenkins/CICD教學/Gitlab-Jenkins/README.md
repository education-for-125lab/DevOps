# Jenkins 與Gitlab CICD

## 前情提要
1. 必須要有gitlab 與 Jenkins
2. 有docker
3. Jenkins 需先安裝這些套件
```
GitLab API Plugin
GitLab Authentication plugin
GitLab Plugin
Docker API Plugin
Docker Commons Plugin
Docker Pipeline
Docker plugin
Matrix Authorization Strategy Plugin
```
4. 以上實作本人都是架在自己的NAS Gitlab是安裝在k3s  Jenkins安裝在docker  (Jenkins安裝再docker上最大優點是方便建立又輕鬆學習) 

## GitLab 前置步驟
1. 先將token 建立好,建立這兩個COM-git-push-token , Jenkins-Gitlab-API-Access(兩個token都要記好,他只會出現1次)

<img src="images/gitlab-3.png" >

2. push 上去 test-python 專案 (第一次跟你電腦push 會跳出一個小視窗填入你的gmail 跟第1步驟要求的token COM-git-push-token)
3. push 好後,gitlab 前置步驟大致完成

## Jenkins 前置步驟
1. 將剛剛gitlab申請的 Jenkins-Gitlab-API-Access 放在Gitlab API token 資訊主頁>管理 Jenkins>Credentials>System>Global credentials (unrestricted)然後 GitLab API token 

<img src="images/gitlab-1.png" >

2. 資訊主頁>管理 Jenkins>System 找到Gitlab

<img src="images/gitlab-2.png" >

3. 若是專案設定為私人(private)建立 Git 用的憑證（Username with password）
4. Jenkins → Manage Jenkins → Credentials → 點選你使用的範圍（例如 `(global)`）
5. 新增憑證：
   - 類型：`Username with password`
   - Username：你在 GitLab 的帳號（例如 qi_0527）
   - Password：剛剛建立的 Personal Access Token
   - ID：例如 `gitlab-https-token`
   - 描述：`GitLab HTTPS Token for clone`

<img src="images/gitlab-15.png" >

6. 記得修改Jenkinfile (credentialsId: 'gitlab-https-token')
```
stage('拉取或更新專案') {
            steps {
                script {
                    echo "=== 檢查是否已經存在專案目錄 ==="
                    withCredentials([usernamePassword(credentialsId: 'gitlab-https-token', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        def repoUrl = "https://${GIT_USERNAME}:${GIT_PASSWORD}@gitlab.qi0527.com/qi_0527/home-webpage.git"

                        if (!fileExists("${PROJECT_DIR}")) {
                            echo "專案目錄不存在，正在拉取專案..."
                            sh "git clone ${repoUrl} ${PROJECT_DIR}"
                        } else {
                            echo "專案目錄已存在，正在更新..."
                            dir("${PROJECT_DIR}") {
                                sh "git remote set-url origin ${repoUrl}"
                                sh "git pull"
                            }
                        }
                    }
                }
            }
        }
```



## Jenkins 創建作業 
1. 選擇 Pipeline
2. Use alternative credential 新增剛剛jenkins創建的Gitlab API token

<img src="images/gitlab-4.png" >

3. 這個記得打勾Build when a change is pushed to GitLab. GitLab webhook URL 並複製它給的 webhook URL 

<img src="images/gitlab-5.png" >

4. SCM 選擇Git 並將你專案的URL 貼上
5. Branches to build (記得是main 不是master,依照你的專案放在哪個路徑)

<img src="images/gitlab-6.png" >

6. Script Path 是 Jenkinsfile ,以上用好就可以存取

<img src="images/gitlab-7.png" >

7. 確認你的 Jenkins是否有 docker CLI

進入jenkins容器
```
docker exec -it jenkins bash
```
確認 docker CLI
```
docker ps

```
沒有就安裝
```
apt-get update
apt-get install -y docker.io

```
8. 還有確定是否安裝 Python , 一樣在jenkins容器
安裝Python 
```
apt update && apt install -y python3 python3-venv python3-pip
```
查看python 版本
```
python3 --version
```
或者看所有版本
```
ls /usr/bin/python*

python3 --version
python3.11 --version
python3.9 --version

```
如果你的系統沒有 python 指令，你可以手動建立一個符號連結，讓 python 指向 python3：
```
ln -s /usr/bin/python3 /usr/bin/python

python --version

```
9. 以上設定完成 到Gitlab 你的專案的Webhook 剛剛Jenkins複製的URl

<img src="images/gitlab-8.png" >

10.  勾選這幾個 然後(SSL verification 看你有沒有申請,我是先不勾)

<img src="images/gitlab-9.png" >

11. 設置使用者權限 (匿名使用者要特別注意,這邊的勾選是給gitlab webhook用的)

<img src="images/gitlab-14.png" >


12.  以上全部完成Jenkins 就可以建置 ,結果如下

<img src="images/gitlab-10.png" >

13. 如果只要有push webhook 有正常通知Jenkins 拉取資料 的話就會顯示200綠色

<img src="images/gitlab-11.png" >

14. Jenkins有自動部署到Docker 

<img src="images/gitlab-13.png" >

<img src="images/gitlab-12.png" >

## 問題區

1. 若是專案設定為私人,並且Jenkins 已經有設定gitlab-https-token 
2. 修改 Jenkins Job Git 設定

3. 到你出錯的 Jenkins Job
4. 找到「Source Code Management」→ 選擇 `Git`
5. URL 用：
   ```
   https://gitlab.qi0527.com/qi_0527/home-webpage.git
   ```
6. Credentials：選剛剛建立的 `gitlab-https-token`


## 以上完成其實只是基礎CICD ,CICD 是還需要自動化測試的