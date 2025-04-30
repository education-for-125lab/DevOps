# Python CICD 

## 前提
1. 須有插件 Allure
2. 需安裝好Jenkins 


## Pytest 簡介

pytest 是一個功能強大又易用的 Python 自動化測試框架，主要用於單元測試與整合測試。它支援自動尋找測試、使用簡潔的 `assert` 斷言語法，並可透過豐富的插件（如 Allure、pytest-cov）擴充功能，是 Python 專案中最受歡迎的測試工具之一。

## 教學
1. 專案檔案結構圖如下
```
devops-test/
├── .idea/                  ← 開發環境設定（例如 PyCharm）(會自動產生不需要自己用)
├── .pytest_cache/          ← pytest 快取資料            (會自動產生不需要自己用)
├── templates/              ← HTML 模板資料夾（Flask 用）
├── test/                   ← 測試資料夾
│   ├── .pytest_cache/      ← pytest 測試快取（子資料夾） (會自動產生不需要自己用)
│   ├── __pycache__/        ← Python 編譯快取            (會自動產生不需要自己用)
│   └── test_app.py         ← 測試程式（pytest 測試）
├── __pycache__/            ← 主程式的 Python 編譯快取    (會自動產生不需要自己用)
├── app.py                  ← 主程式檔案（Flask 應用）
├── Dockerfile              ← 建構 Docker 映像用
├── Jenkinsfile             ← Jenkins CI/CD 腳本
└── requirements.txt        ← Python 相依套件清單

```
2. 以下程式檔

Project Name/app.py

```
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def home():
    return render_template('index.html')

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5500)

```

Project Name/templates/index.html

```
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>測試Jenkins CICD 自動部署</title>
    <!-- 引入 Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">

    <nav class="navbar navbar-dark bg-primary">
        <div class="container">
            <a class="navbar-brand" href="#">測試 DevOps</a>
        </div>
    </nav>

    <div class="container text-center mt-5">
        <h1 class="display-4 text-primary">歡迎來到 測試 DevOps</h1>
        <p class="lead">這是一個簡單的 Gitea 與 Jenkins 和 Docker 架設</p>

        <div class="row mt-4">
            <div class="col-md-4">
                <div class="card shadow-sm">
                    <div class="card-body">
                        <h5 class="card-title">Gitea</h5>
                        <p class="card-text"> 輕量級 Git 伺服器適合自架 </p>
                        <a href="https://gitea.io/" target="_blank" class="btn btn-primary">瞭解更多</a>
                    </div>
                </div>
            </div>
            <div class="col-md-4">
                <div class="card shadow-sm">
                    <div class="card-body">
                        <h5 class="card-title">Jenkins</h5>
                        <p class="card-text">CI/CD 自動化部署工具 </p>
                        <a href="https://www.jenkins.io/" target="_blank" class="btn btn-success">瞭解更多</a>
                    </div>
                </div>
            </div>
            <div class="col-md-4">
                <div class="card shadow-sm">
                    <div class="card-body">
                        <h5 class="card-title">Docker</h5>
                        <p class="card-text">容器化技術，讓部署更簡單 </p>
                        <a href="https://www.docker.com/" target="_blank" class="btn btn-info">瞭解更多</a>
                    </div>
                </div>
            </div>
            <div class="col-md-4">
                <div class="card shadow-sm">
                    <div class="card-body">
                        <h5 class="card-title">基本講解DevOps</h5>
                        <p class="card-text">CI/CD 自動化部署工具 </p>
                        <a href="https://gitea.qi0527.com/qi/Basic-Teaching/src/branch/main/DevOps" target="_blank" class="btn btn-primary">瞭解更多</a>
                    </div>
                </div>
            </div>
            <div class="col-md-4">
                <div class="card shadow-sm">
                    <div class="card-body">
                        <h5 class="card-title">個人的網頁</h5>
                        <p class="card-text">個人的首頁</p>
                        <a href="https://qi0527.com/#gsc.tab=0" target="_blank" class="btn btn-success">瞭解更多</a>
                    </div>
                </div>
            </div>
            <div class="col-md-4">
                <div class="card shadow-sm">
                    <div class="card-body">
                        <h5 class="card-title">個人教學網</h5>
                        <p class="card-text">基本自我教學Gitea</p>
                        <a href="https://gitea.qi0527.com/qi" target="_blank" class="btn btn-info">瞭解更多</a>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

```

Project Name/test/test_app

```
import sys
import os
# 添加 devops-test 目錄到 sys.path，這樣可以讓 Python 正確找到 app.py
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '..')))

import pytest
from app import app

@pytest.fixture
def client():
    app.config['TESTING'] = True
    with app.test_client() as client:
        yield client

def test_hello():
    assert 1 == 1

def test_home_page(client):
    """測試首頁是否成功回傳 200 且包含預期中文字"""
    response = client.get('/')
    assert response.status_code == 200

    # 解碼 HTML 並進行中文內容驗證
    html = response.data.decode('utf-8')
    assert "歡迎來到 測試 DevOps" in html
    assert "Gitea" in html
    assert "Jenkins" in html
    assert "Docker" in html

```

Project Name/Dockerfile

```
# 使用官方 Python 3.11 作為基礎映像
FROM python:3.11-slim

# 設定工作目錄
WORKDIR /app

# 複製 requirements.txt 並安裝依賴
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 複製應用程式檔案
COPY . .

# 設定 Flask 應用程式的執行命令
CMD ["python", "app.py"]

```

Project Name/Jenkinsfile

```
pipeline {
    agent any

    environment {
        PROJECT_DIR = "${env.WORKSPACE}/devops-test"
        VENV_DIR = "${PROJECT_DIR}/venv"
        IMAGE_NAME = 'devops-test-image'
        CONTAINER_NAME = 'devops-test-container'
        ALLURE_RESULTS_DIR = "${PROJECT_DIR}/allure-results"
        ALLURE_REPORT_DIR = "${PROJECT_DIR}/allure-report"
    }

    stages {
        stage('檢查工作目錄') {
            steps {
                script {
                    echo "當前目錄: ${pwd()}"
                }
            }
        }

        stage('檢查 Python 版本') {
            steps {
                sh 'python3 --version || { echo "Python3 未安裝，請確認系統環境！"; exit 1; }'
            }
        }

        stage('安裝 pip') {
            steps {
                sh 'apt-get update && apt-get install -y python3-pip'
            }
        }

        stage('拉取或更新專案') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'gitlab-https-token', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        def repoUrl = "https://${GIT_USERNAME}:${GIT_PASSWORD}@gitlab.qi0527.com/qi_0527/devops-test.git"
                        
                        if (!fileExists("${PROJECT_DIR}")) {
                            sh "git clone ${repoUrl} ${PROJECT_DIR}"
                        } else {
                            dir("${PROJECT_DIR}") {
                                sh "git remote set-url origin ${repoUrl}"
                                sh "git fetch origin"
                                sh "git checkout -B main origin/main"
                                sh "git reset --hard origin/main"
                            }
                        }
                    }
                }
            }
        }

        stage('設定虛擬環境') {
            steps {
                script {
                    // 檢查虛擬環境目錄是否已存在
                    if (!fileExists("${VENV_DIR}/bin/activate")) {
                        echo "虛擬環境不存在，創建虛擬環境..."
                        sh "python3 -m venv ${VENV_DIR}"
                    } else {
                        echo "虛擬環境已存在，跳過創建步驟"
                    }
                }
            }
        }

        stage('安裝依賴') {
            steps {
                script {
                    def reqFile = "${PROJECT_DIR}/requirements.txt"
                    sh ". ${VENV_DIR}/bin/activate && pip install --upgrade pip"
                    if (fileExists(reqFile)) {
                        sh ". ${VENV_DIR}/bin/activate && pip install -r ${reqFile}"
                    } else {
                        sh ". ${VENV_DIR}/bin/activate && pip install flask pytest allure-pytest"
                    }
                }
            }
        }

        stage('清理並創建 Allure 結果目錄') {
            steps {
                sh "rm -rf ${ALLURE_RESULTS_DIR} || true"
                sh "mkdir -p ${ALLURE_RESULTS_DIR}"
            }
        }

        stage('執行測試') {
            steps {
                dir("${PROJECT_DIR}") {
                    script {
                        def testStatus = sh(
                            script: ". ${VENV_DIR}/bin/activate && pytest -v --alluredir=${ALLURE_RESULTS_DIR} test/",
                            returnStatus: true
                        )
                        echo "測試結束，狀態碼：${testStatus}"
                        if (testStatus != 0) {
                            currentBuild.result = 'UNSTABLE'
                        }
                    }
                }
            }
        }

        stage('生成 HTML Allure 測試報告') {
            steps {
                dir("${PROJECT_DIR}") {
                    sh "/opt/allure-2.33.0/bin/allure generate ${ALLURE_RESULTS_DIR} -o ${ALLURE_REPORT_DIR} --clean"
                    sh "ls -la ${ALLURE_REPORT_DIR}"
                }
            }
        }

        stage('構建 Docker 映像') {
            steps {
                sh "docker build --no-cache -t ${IMAGE_NAME} ${PROJECT_DIR}"
            }
        }

        stage('移除舊容器') {
            steps {
                sh "docker stop ${CONTAINER_NAME} || true"
                sh "docker rm ${CONTAINER_NAME} || true"
            }
        }

        stage('啟動新容器') {
            steps {
                sh "docker run -d --restart=always --name ${CONTAINER_NAME} -p 5500:5500 ${IMAGE_NAME}"
            }
        }

        stage('部署完成') {
            steps {
                echo "部署完成！"
            }
        }
    }

    post {
        always {
            echo "清理工作完成"
        }

        success {
            echo "成功完成 Allure Report"

            // 顯示 Allure Report（Jenkins 插件）
            allure([
                reportBuildPolicy: 'ALWAYS',
                results: [[path: "devops-test/allure-results"]]
            ])

            // 顯示 HTML Report（HTML Publisher Plugin）
            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: "devops-test/allure-report",
                reportFiles: 'index.html',
                reportName: 'Allure HTML Report'
            ])
        }

        failure {
            echo "失敗，請檢查錯誤日誌"
        }
    }
}

```

Project Name/requirements

```
Flask
pytest
allure-pytest

```
3. Pipeline 跟前面 CICD 教學一樣
4. 結果如下
Gitlab

<img src="images/gitlab-16.png" >

Gitlab Webhooks

<img src="images/gitlab-17.png" >

Jenkins 

<img src="images/gitlab-18.png" >

Allure

<img src="images/gitlab-19.png" >


<img src="images/gitlab-20.png" >


<img src="images/gitlab-21.png" >

