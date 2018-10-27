# 獲得所需的驅動程式（kext）

## 我需要哪些驅動程式？

首先我們必須要VirtualSMC.kext，他負責模擬真的Mac電腦裡的SMC晶片，用來偽裝成一台真正的Mac電腦。也就是說，如果沒有了VirtualSMC.kext的話，就無法啟動macOS。

所有必須的驅動程式都可以在 [這個由Goldfish64所建立的倉庫中](https://1drv.ms/f/s!AiP7m5LaOED-m-J8-MLJGnOgAqnjGw) 找到 。 當中的每一個驅動程式式都會在有新的變動（Commit）時發布一次更新。

當然如果你喜歡有自己來編譯這些驅動程式的話，你可以參考一下[這個腳本 ](https://github.com/corpnewt/Lilu-and-Friends)。

### 網卡：

* \_\_[_IntelMausiEthernet.kext_ ](https://github.com/Mieze/IntelMausiEthernet)- 適用於現在主流的Intel網卡晶片。
* _AppleIntelE1000e.kext_ - 適用於較久的Intel網卡晶片。- 但是當使用在較新的Intel網卡晶片時，有機會使macOS Kernel Panic。
* \_\_[_AtherosE2200Ethernet.kext_ ](https://github.com/Mieze/AtherosE2200Ethernet)- Atheros和Killer網卡晶片適用。
* \_\_[_RealtekRTL8111.kext_](https://github.com/Mieze/RTL8111_driver_for_OS_X) - 適用於Realtek千兆網卡晶片。
* \_\_[_RealtekRTL8100.kext_ ](https://github.com/Mieze/RealtekRTL8100)- 適用於  Realtek 10/100M 網卡晶片

### USB：

你將會需要 [_USBInjectAll.kext_](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/)。 如果你的主板是使用 H370, B360 和 H310 等Coffee Lake CPU 架構的話，要再加上 _XHCI-**3**00-series-injector.kext_。 X79/X99/X299 就需要 _XHCI-x99-injector.kext_ 。

這三個驅動程式都可以在USBInjectAll.kext的倉庫中找到。

 在Mac OS X El Captian上， 蘋果在USB控制器上設置了不能超過15個USB端口的設定。儘管聽起來沒什麼大不了，但是其實是不夠的-----因為每個USB 3.0的端口會被當成兩個USB端口：一個是USB 2.0使用，一個是給USB 3.0。 而Skylake架構就因為USB 2.0和USB3.0都是由XHCI控制器控制，因此更快會用滿15個USB端口的限制。因此這個時候我們需要 [RehabMan的FakePCIID.kext + FakePCIID\_XHCIMux.kext](https://github.com/RehabMan/OS-X-Fake-PCI-ID) ，將USB2.0的端口改由EHCI控制器所管理，由此就可以繞過15個USB端口的限制了。

### 聲卡：

Reddit用戶/u/vit9696的 [_AppleALC.kext_](https://github.com/vit9696/AppleALC/releases) 和 [_Lilu.kext_](https://github.com/vit9696/Lilu/releases) - 它們會為你的聲卡提供 [macOS所適用的編碼](https://github.com/vit9696/AppleALC/wiki/Supported-codecs)。 當中_AppleALC.kext_ 是用作為 _AppleHDA.kext_ 打上補丁的驅動程式。它也可以支援在休眠後使用聲卡的編碼參數。

### 顯示卡：

 可以下載 [_WhateverGreen.kext_](https://github.com/acidanthera/WhateverGreen/releases) 和 [_Lilu.kext_](https://github.com/vit9696/Lilu/releases) - （如果前面已下載Lilu.kext的話則不需要再次下載。）當中WhateverGreen.kext已包含了以前 _IntelGraphicsFixup.kext_, _NvidiaGraphicsFixup.kext_, _CoreDisplayFixup.kext_, and _Shiki.kext_ 的功能，也就是說現在只需要這個驅動程式,就可以為你的AMD/Nvidia/Intel iGPU顯示卡打上補丁。

### WiFi 和藍牙：

Apple 對於Wifi晶片的支援很少, 因此這裡就舉些例子.  

例如本文檔的創作者CorpNewt，使用的是 BCM94360CD晶片的PCIe網卡，以及 BCM94352HMB/BCM94352Z.  當中 BCM94360CD 因為獲得了原生支援，所以不用再額外的設定就能使用。 但是BCM94352就要安裝 [_AirportBrcmFixup.kext_ ](https://github.com/acidanthera/AirportBrcmFixup) （需要Lilu.kext） 和 _BrcmBluetoothInjector.kext_ \(macOS 10.13.6或以上\) ，或者 _BrcmPatchRAM2.kext_ 以及 _BrcmFirmwareData.kext_ 

所有Boardcom Wifi晶片的驅動程式都可以在[ RehabMan的 _OS-X-BrcmPatchRAM_](https://github.com/RehabMan/OS-X-BrcmPatchRAM) 倉庫中找到。

Wifi晶片建議先Google/百度一下你的Wifi晶片型號是否被macOS支援，可以節省不少時間。

還有你可以在淘寶上花200多久可以有一個兼容macOS黑蘋果的WiFi PCIe卡。

### 額外的硬件：

要自己去Google/百度一下了。

