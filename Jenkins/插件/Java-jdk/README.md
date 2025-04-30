# 安裝Java
可以先創建一個java資料夾 cd java ,安裝完就可以刪除
```
wget https://download.oracle.com/java/21/latest/jdk-21_linux-x64_bin.deb
sudo dpkg -i jdk-21_linux-x64_bin.deb

```
驗證安裝
```
java -version
```
```
qi@omvnas:/home/primary-drive/DevOps/Allure$ java -version
java version "21.0.6" 2025-01-21 LTS

```
如果你java很多版本可以用以下選擇
```
sudo update-alternatives --config java
```
```
qi@omvnas:/home/primary-drive/DevOps/Allure$ sudo update-alternatives --config java
替代項目 java（提供 /usr/bin/java）有 1 個選擇。

  選項       路徑                                       優先權  狀態
------------------------------------------------------------
* 0            /usr/lib/jvm/jdk-21.0.6-oracle-x64/bin/java   352370688 自動模式
  1            /usr/lib/jvm/jdk-21.0.6-oracle-x64/bin/java   352370688 手動模式

按下 <enter> 鍵以保留目前選項[*]，或是輸入選擇編號：

```

1. 我們需要設置 JAVA_HOME 環境變數：
   ```bash
   nano ~/.bashrc
   ```

2. 在文件的底部加入以下內容，將 `JAVA_HOME` 指向正確的 Java 安裝路徑（這裡我們假設你安裝的是 OpenJDK 21）：
   ```bash
   export JAVA_HOME=/usr/lib/jvm/jdk-21.0.6-oracle-x64
   export PATH=$JAVA_HOME/bin:$PATH
   ```

3. 儲存並關閉文件，然後執行以下命令來使改動生效：
   ```bash
   source ~/.bashrc
   ```

4. 確認 `JAVA_HOME` 是否正確設置：
   ```bash
   echo $JAVA_HOME
   ```
結果
```
qi@qi0527:~$ echo $JAVA_HOME
/usr/lib/jvm/jdk-21.0.6-oracle-x64
qi@qi0527:~$ 

```
5. 安裝好後回到Jenkins 將 jdk加入
6. 點擊 管理Jenkins> Tools
7. 依照自己的 JAVA_HOME 設定
```
JDK 安裝 
名稱: Java-jdk
JAVA_HOME: /usr/lib/jvm/jdk-21.0.6-oracle-x64
```