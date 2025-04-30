# Tailscale 架設

## 簡介
Tailscale 是一款基於 WireGuard 的點對點虛擬私人網路（VPN）解決方案。它可以讓你的裝置之間透過加密的連線安全地通訊，無論裝置身處何處。Tailscale 主打「零設定」連線，適用於個人、團隊及企業使用，並支援多種平台（如 Windows、macOS、Linux、iOS、Android）。其架構避免傳統 VPN 的複雜配置與中央伺服器依賴，實現快速、安全、易用的內網連線體驗。


## 安裝
1. 註冊好帳號(https://login.tailscale.com/login?next_url=%2Fwelcome)
2. 安裝後設定開機自動啟用Tailscale服務
```
sudo systemctl enable --now tailscaled

# 檢查服務狀態
sudo systemctl status tailscaled

```
3. 將裝置加入Tailscale網路
```
sudo tailscale up

```
4. 網頁中DNS區記得開啟這些MagicDNS,HTTPS Certificates 通常他會自動開啟
5. 以上完成選擇你想開啟的端口 (就可以瀏覽器輸入 Tailscale 給的ip 加 你要端口 例: http://100.75.50.89:5500/)



## Tailscale funnel 用法
1. 任意用戶開放服務 Tailscale 會給你一個網址,那個網址就是你隨史都可以用不需要vpn 或加入裝置設備
```
sudo tailscale funnel 8668

```
結果
```
https://qi0527.manee-ilish.ts.net/
|-- proxy http://127.0.0.1:8668

Press Ctrl+C to exit.
```
2. 背後執行tailscale funnel ,不會關掉
```
sudo nohup tailscale funnel 8668 &
sudo cat nohup.out
```
結果
```
qi@qi0527:~$ sudo cat nohup.out
Available on the internet:

https://qi0527.manee-ilish.ts.net/
|-- proxy http://127.0.0.1:8668

Press Ctrl+C to exit.
qi@qi0527:~$ 

```