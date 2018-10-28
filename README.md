# 快速入門

## 原生Mac OS 安裝體驗教學

#### 什麼意思?

原生安裝體驗就是在不修改原廠映像檔的前提下，將所有和黑蘋果有關的kexts，補丁，驅動程式等等放在EFI分割區裡。因此正常情況下，通過此模式安裝Mac OS的，基本上和一台真正的蘋果電腦 _一模一樣_。

**詞彙**

這裡有些在這個教學裡會經常看到的詞彙：


* _Clover_ - 這個應該有碰過黑蘋果的都應該知道，是啟動黑蘋果必須要的載入程式。Clover 能完整的模擬真正蘋果電腦的 EFI 框架，因此由Clover 引導 OS X 就能產生一台如同 MAC + OS X 完整體驗的個人電腦。它負責kext注入，修改ACPI名稱，為kext進行補丁等等的工作。詳細可以看 https://goo.gl/9esK8S 。

* _Kexts_ - 其實是 **K**ernel **Ext**ension（內核外掛（HKEAA對於擴充程式的寫法）程式）的縮寫，簡單來講就是macOS的驅動程式，附檔名為.kext。

* _Config.plist_ - 這是Clover的配置檔案，以XML（一款類似html的表示語言）格式儲存。因為黑蘋果能不能啟動就看是否正確配置Config.plist,，所以基本上本教程所有的重點都是在它上。

* _OOB_ -  _Out Of the Box_ （開箱即用）的縮寫，也就是說在不用修改的情況下，就能直接使用。

* _KP_ /_Kernel Panic_ - 就是Windows 平台上所謂的“藍屏”。

詞彙表將會持續更新。

## 注意事項+準備

本教學僅適用於Intel CPU的桌上型電腦。筆記本的同學們可以出門左轉參閱在tonyMacx86網站上[RehabMan的教學](https://www.tonymacx86.com/threads/guide-booting-the-os-x-installer-on-laptops-with-clover.148093/) 

注意：筆記本的安裝會 _因型號_ 而有所差異。

事前準備：

1. 一隻8GB以上的USB記憶棒。

2. Install OS X/macOS.app 程式。\(建議從App Store上下載，後面的文章也有鏡像鏈接。\)

3. [Clover 安裝包](https://github.com/Dids/clover-builder/releases) (由Dids提供。\)

4. [Clover 編輯器 (Clover Configrator)](http://mackie100projects.altervista.org/download-clover-configurator/) \(有經驗的用家可以直接使用任何文本編輯器修改，但對於一般使用者而言，Clocver Configrator更
方便和上手使用。\)
   * 記得是下載 _Global_ 版。

5. [_VirtualSMC.kext_](https://github.com/acidanthera/VirtualSMC/releases) - 這個應該有碰過黑蘋果的都應該會問：不是 _FakeSMC.kext_ 嗎？現在我們會使用   VirtualSMC.kext 作為SMC模擬器。這是整個黑蘋果安裝中最重要的驅動程式，沒有了它，就無法啟動我們的安裝盤，跟別提macOS開機了。

6. 各個硬件需要用到的kext。

7. 耐性, 毅力, 和懂得使用Google/百度。
  （建議使用Google搜尋，比較多資源。）

* 好了，現在我們可以前往[下一步](gethwinfo.md)了！

