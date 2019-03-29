---
description: 安裝ucos所需的環境
---

# 安裝

### μc/os-II

* Windows平台: 如果實驗在PC內進行，需要安裝DOSbox, borland C++以及Ucossource[按此下載](https://drive.google.com/open?id=1BnBroHB3wTsqZh5CyZxON-G3s2CbiqWj)。
* Linux\(Ubuntu\): 透過以下指令安裝:

  ```bash
  sudo apt-get update && sudo apt-get upgrade
  sudo apt-get install dosbox
  ```

  **編譯**

  ```text
  dosbox 
  mount c:
  mount c ~/
  cd \xxx\your_dir\xxx\SOFTWARE\UCOS-II\EX1_x86L\bc45\TEST
  MAKETEST.BAT
  TEST.EXE
  ```

* MAC

  install environment 

* Download DOSbox 

go to choose [DOSbox Official Website](https://www.dosbox.com/)-&gt;Download menu -&gt; choose mac

reference: [「DOSBox」DOS模擬器，玩DOS經典遊戲的好幫手！](http://it-easy.tw/dosbox/) :+1:

1. Download borland C++以及Ucossource

put SOFTWARE/bc45 directory into ~/Desktop

1. open 

   ```text
   mount c ~/Desktop
   C:
   cd  \SOFTWARE\UCOS-II\EX1_x86L\bc45\TEST
   MAKETEST.BAT
   TEST.EXE
   ```

