---
title: 在 macOS 上使用 TouchID 來驗證 sudo 密碼
categories: macOS
toc: false
date: 2021-07-25 21:17:16
tags:
  - macOS
  - TouchID
---
當 mac 密碼安全性很高 ~~(aka 又臭又長)~~ 時，sudo 打起密碼來就比較費時 {% asset_img inline sudo-request-password.png 480 %}

因此我們可以使用 TouchID 來取代這件事
<!--more-->

1. 透過 Terminal 編輯 `/etc/pam.d/sudo`
   ```shell
   $ sudo vi /etc/pam.d/sudo
   ```
   或許這是最後一次在 Terminal 輸入 sudo 密碼了 😆
   Note: _PAM = **P**luggable **A**uthentication **M**odule_
2. 檔案看起來應該會像下面這樣，列出所有 sudo 可用的驗證方式
   ```shell
   # sudo: auth account password session
   auth       sufficient     pam_smartcard.so
   auth       required       pam_opendirectory.so
   account    required       pam_permit.so
   password   required       pam_deny.so
   session    required       pam_permit.so
   ```

   我們在檔案第二行加入 TouchID (下方的 `pam_tid.so`)
   ```shell
   # sudo: auth account password session
   auth       sufficient     pam_tid.so
   auth       sufficient     pam_smartcard.so
   auth       required       pam_opendirectory.so
   account    required       pam_permit.so
   password   required       pam_deny.so
   session    required       pam_permit.so
   ```
3. 接著存檔離開 (先按 `Esc` 後，再按 `wq!`)
4. 開啟新的 Terminal 視窗確認結果 🎉
   {% asset_img inline sudo-request-touchid.png 480 %}
   當然這時候如果按了 Cancel，那一樣會彈出原本要你手動輸入密碼的提示
