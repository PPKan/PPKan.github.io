---
layout: post
title: "Proxmox Virtual Environment + OpenVPN 架設 write-up"
subtitle: ""
date: 2025-01-27
author: "Peter"
header-img: "img/post-bg-hack.jpg"
tags: [資安, 網路, VPN]
---

日安，本篇文章的目的是紀錄 Proxmox VE 與 OpenVPN 架設的步驟，Proxmox VE 作為自架環境的好工具，但作為滲透練習的話還是要把網路區隔做好，也就用了 OpenVPN 做後續的設定。

這篇文章的主要目的是先寫起來放，怕之後要重新架的時候忘記。，順便也梳理一下過程中理解的東西。架設過程中大幅度地運用了 ChatGPT-O1，獲益良多，但過程中的問題是一味地照本宣科很難把被告知知識內化，其次是就算是 O1 很難在指點之前給予足夠的藍圖，這篇文章會嘗試以比較高角度的方式去重新剖析網路拓樸的過程。

內容主要分成兩個大區塊，Proxmox 的架設與後續利用 OpenVPN 連到虛擬機的過程，前面部分稍微容易一些也寫得很籠統鑒於 Proxmox 的設定都蠻直覺的，唯獨 wifi 設定需要一些手續但也不是太困難。而 OpenVPN 的設定就相對比較複雜，也是我相對陌生的部分，這個部分用了一些圖表與指令，裡面或多或少會有些遺漏，寫好之後還沒有把電腦還原重新 run 過，之後有機會重 run 的話會再增添內容。

在正文之前想講講架設這個的原因，最近想要開始在自架環境中練習紅藍隊的內容，我相信多數人應該都是用自己的電腦跑 VMware。無奈過年期間手邊只有一台輕薄筆電跟一台稍老一點的電競筆電，輕薄筆電是我的外出日常機，沒辦法雙開 Kali 跟 Windows，這邊就用了老舊的筆電架設了 Proxmox 來跑虛擬 VM 的環境。

> 最根本的原因是雲端伺服器實在太貴了

## Proxmox 架設

Proxmox 的架設過程很容易，找一個 USB 把 ISO 燒錄到裡面即可。具體步驟可參考官方文件 [Installing Proxmox VE](https://pve.proxmox.com/pve-docs/chapter-pve-installation.html)，這邊就不多贅述。

安裝完後把電腦從 BIOS 開機後需要注意的點是網路，Proxmox 是沒有 GUI 的 OS，在開機之後會彈出一個網址如下圖，圖片來源：[Install Proxmox VE {Step-by-Step Guide}](https://phoenixnap.com/kb/install-proxmox)

![Proxmox welcome output.](https://phoenixnap.com/kb/wp-content/uploads/2022/01/proxmox-welcome-output.png)

這個步驟要特別注意的點是，**電腦如果不先聯網後面設定就會很麻煩**，在安裝過程中會有網路設定，如果這個階段沒有就連接到有設備可以存取的網段的話安裝起來麻煩就很多，除此之外都還好。

### Wifi 設定

wifi 設定參考：[TUTORIAL\ - HOWTO - Proxmox VE 8-1.2 Wifi w/ SNAT Proxmox Support Forum](https://forum.proxmox.com/threads/howto-proxmox-ve-8-1-2-wifi-w-snat.142831/)，內容照著跑都不會有問題，不過文章中沒有提到的是，如果你要讓上面的 Welcome message 顯示網址更改的話，要跟著修改 `/etc/hosts` ，改完就好了。

### 將 ISO 檔存入伺服器

設定完 OS 後，我們要來進行最重要的建立 VM，但在建立 VM 之前，比較好的習慣是將 ISO 檔先傳入伺服器中，讓伺服器可以穩定的有個 ISO 以備不時之需。

我們這邊假設已經有掛載了一個硬碟在 `/mnt/hdd` ，我們先在硬碟中建立一個資料夾

```
mkdir -p /mnt/hdd/iso
```

後面去 Proxmox 的 UI 中的 `Datacenter > Storage` 中新增資料夾存放區。如果有其他的檔案要新增的話同樣也是從這邊開資料夾。

![image-20250125212505613](/img/in-post/proxmoxvewriteup/image-20250125212505613.png)

在開完之後從左邊標籤進去就可以上傳 ISO 檔了。

![4123](/img/in-post/proxmoxvewriteup/4123.png)

### 網路設定（重要）

在建立 VM 的時候我們可以選擇讓他斷網或是不斷網，在這邊我建議先把網路設定好，建立好 VM 後就直接用內網設定去走。我們這邊在 host 上開一個 `10.10.0.1/24` 的網路區段，由於網段我們不做 NAT 的關係不會上到外網，但在後續我們因為還是要做網段區隔，所以後面同樣會開 forward，但只會 forward 到我們 OpenVPN 所使用的網段。

![213213](/img/in-post/proxmoxvewriteup/213213.png)

### 建立 VM

右上角有一個藍色的 Create VM，照著做基本上不會有什麼問題，CPU、RAM 等等就依照自己需求設定。要注意的是 windows VM 在建立之後要灌驅動，具體參考：[Paravirtualized Block Drivers for Windows - Proxmox VE](https://pve.proxmox.com/wiki/Paravirtualized_Block_Drivers_for_Windows#Setup_During_Windows_Installation)。後面把 ISO 檔掛載進去即可。

![image-20250125213907389](/img/in-post/proxmoxvewriteup/image-20250125213907389.png)

到這個步驟我們的 windows 就基本上已經設定好了，接下來要準備的是設定 OpenVPN 來讓我們的 Kali 可以連過去。

## OpenVPN 架設

在上一個階段，我們成功地架設了 Proxmox VE 的 OS 以及建立了一個 Windows 的 VM，但我們目前還沒有辦法將我們的攻擊機連上去，為了連上我們還要設定 OpenVPN，不過大家可能會想說明明直接把 VM 掛在內網就好了，為什麼要特地這樣做，我想聊聊這樣做可能的好處：

1. 避免接觸外網（WAN），在做滲透測試練習的時候，我們多半不希望我們的虛擬機是可以上網的狀態，如果要上網做一些設定的話可以另外再弄。
2. 如果單純跟所有設備處在同一個內網環境用防火牆去做分割也不是不行，但所有設備混在同一個網段下就會顯得挺亂的，而且一個沒設定好就很容易亂套，並且，這樣的情況下其他任意的設備也都可以直接存取到我們的虛擬機。
3. 更多的區段分割，如果將網路分開的話可以讓 VM 們有個更好的環境相互溝通。這邊的做法是連 VPN 上的 Kali 也不會跟 VM 待在同一個網路下，而是走 OpenVPN 的 post routing 。

網路結構如果整理成圖的話會像下面這樣，我們的 Kali Client 跟 Proxmox VE 伺服器會同處 OpenVPN 的主要伺服器 `10.8.0.0/24`，但我們的 VM 會設定在上面設定的 `vmbr1` 也就是 `10.10.0.0/24` 裡面，這邊會透過 OpenVPN 的 pust route 的方式將 `10.8.0.0/24` 的網路 route 至 `10.10.0.0/24` 。

![openvpn-topology](/img/in-post/proxmoxvewriteup/openvpn-topology.png)

搞清楚上面邏輯的話，我們要做的事情就會很明確

1. 設定 Proxmox VE 網段至 `10.10.0.0/24`

2. 設定 OpenVPN

   a. 架設 VPN 伺服器與設定，主伺服器為 `10.8.0.0`，並要將 `10.8.0.0` 經由 OpenVPN push route 至 `10.10.0.0`

   b. 設定 VM 的 IP 與 gateway 分配

   c. 建立連線用的憑證，讓 Kali 可以連線過去

### 設定 OpenVPN 伺服器與設定

有鑑於 Proxmox 的網路設定已經在上面網路設定的部分設定完成了，我們的 Server 跟 VM 這時候都連上了 `vmbr1` 也就是 `10.10.0.0/24` 的網段，我們接下來則需要架設 OpenVPN 伺服器，讓 Kali 可以經由 VPN 連上 VM。

OpenVPN 的設定相對容易，我們首先要先架設伺服器，為了架設伺服器我們需要建立憑證與配置。

```bash
# 安裝必要元件
apt-get update
apt-get install openvpn easy-rsa

# 使用 easy-rsa 建立 OpenVPN 憑證
# 利用 make-cadir 建立憑證的資料夾 (make-ca-directory)
make-cadir /etc/openvpn/easy-rsa
# CD 至該資料夾
cd /etc/openvpn/easy-rsa
# 初始化 PKI 金鑰與建立 CA 憑證，記得打密碼
./easyrsa init-pki
./easyrsa build-ca
# 生成 Server 端憑證與金鑰，nopass 可視需求更改，但為了伺服器部屬便利性這邊沒用
./easyrsa build-server-full server nopass
# 生成 Diffie-Hellman 參數加強安全
./easyrsa gen-dh
```

填入 OpenVPN 伺服器配置

```bash
# /etc/openvpn/server.conf
port 1194
proto udp
dev tun

# 憑證相關位置
ca /etc/openvpn/easy-rsa/pki/ca.crt
cert /etc/openvpn/easy-rsa/pki/issued/server.crt
key /etc/openvpn/easy-rsa/pki/private/server.key
dh /etc/openvpn/easy-rsa/pki/dh.pem

# 指定 VPN 虛擬網段
server 10.8.0.0 255.255.255.0

# 允許用戶端之間互通 (Optional)
client-to-client

# 允許轉發網段
push "route 10.10.0.0 255.255.255.0"

keepalive 10 120
persist-key
persist-tun
comp-lzo no
user nobody
group nogroup
verb 3
```

這邊有幾個點可以說明，首先是憑證是我們上面生成的金鑰與DH，其次是 VPN 的網段，在這邊我們設定了 `10.8.0.0`，最後我們使用 `push "route 10.10.0.0 255.255.255.0"` 讓流量可以順利從 `10.8.0.0` 過到 `10.10.0.0`。

接下來就可以測試了

```bash
systemctl enable openvpn@server
systemctl start openvpn@server
systemctl status openvpn@server
```

這個步驟理應不會出現報錯訊息。

### 設定 Windows VM 的 IP

基本照著下圖設定，重點是手動分配 IP 至 `10.10.0.10`，DNS 可依需求設定，我這邊是 AD DC 的關係所以設定在自己的 IP。

![image-20250127135441079](/img/in-post/proxmoxvewriteup/image-20250127135441079.png)

在這個步驟時，`ipconfig` 應顯示 `10.10.0.10`，且 Proxmox 是可以與 VM 的 IP 互 ping 的。

![image-20250127135829690](/img/in-post/proxmoxvewriteup/image-20250127135829690.png)

![image-20250127135650887](/img/in-post/proxmoxvewriteup/image-20250127135650887.png)

### 設定 OpenVPN 用戶端憑證

我們把伺服器及VM端設定好後，接下來需要進行用戶端，在這邊也就是 Kali Linux 的設定。

```bash
# 同樣使用 easy-rsa 生成 client 憑證，生成後會在 
# /etc/openvpn/easy-rsa/pki/private/kali-client.key
# /etc/openvpn/easy-rsa/pki/issued/kali-client.crt
cd /etc/openvpn/easy-rsa
./easyrsa build-client-full kali-client nopass
```

接下來我們需要手搓 `kali-client.ovpn`，這注意修改的是 remote <IP> <PORT> 記得對齊 OpenVPN 的設定

```
client
dev tun
proto udp
remote 192.168.50.100 1194
resolv-retry infinite
nobind
persist-key
persist-tun
remote-cert-tls server
verb 3
comp-lzo no

<ca>
-----BEGIN CERTIFICATE-----
# /etc/openvpn/easy-rsa/pki/ca.crt
-----END CERTIFICATE-----
</ca>

<cert>
-----BEGIN CERTIFICATE-----
# /etc/openvpn/easy-rsa/pki/issued/kali-client.crt
-----END CERTIFICATE-----
</cert>

<key>
-----BEGIN PRIVATE KEY-----
# /etc/openvpn/easy-rsa/pki/private/kali-client.key
-----END PRIVATE KEY-----
</key>
```

接下來在 kali 安裝 OpenVPN 理應就可以連上了，透過 `traceroute` 驗證網路是經由 OpenVPN 的 `10.8.0.1` 到 `10.10.0.10` 。到這邊我們就透過 OpenVPN 連到虛擬機，完成 Proxmox Virtual Lab 的架設。

```bash
# terminal 1
sudo apt install openvpn
sudo openvpn ./kali-client.ovpn

# terminal 2
$ traceroute 10.10.0.10
1  10.8.0.1 (10.8.0.1)  86.536 ms  88.117 ms  88.125 ms

$ ping 10.10.0.10
PING 10.10.0.10 (10.10.0.10) 56(84) bytes of data.
64 bytes from 10.10.0.10: icmp_seq=1 ttl=127 time=58.2 ms
64 bytes from 10.10.0.10: icmp_seq=2 ttl=127 time=82.8 ms
64 bytes from 10.10.0.10: icmp_seq=3 ttl=127 time=6.08 ms
64 bytes from 10.10.0.10: icmp_seq=4 ttl=127 time=19.7 ms
64 bytes from 10.10.0.10: icmp_seq=5 ttl=127 time=45.2 ms
```

## 結語

十分簡短的寫了一篇關於 Proxmox VE 與 OpenVPN 的設定，內容主要是梳理安裝過程不懂的地方，並嘗試將過程簡潔與圖像化。裡面關於網路拓樸的部分相對是學習到比較多的地方，但就如同最上面所說的，網路設定這塊我是相對陌生的，有錯誤的地方還請多包涵，共同努力 😉👍。

---

感謝閱讀。本文同步發表於FB粉絲專頁 [PP學習筆記](https://www.facebook.com/pplearningnote)，歡迎分享與留言討論。