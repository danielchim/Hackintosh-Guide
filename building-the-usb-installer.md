# 製作USB安裝盤

當你準備好你的8GB或以上的USB記憶棒後，我們就可以開始製作macOS USB安裝盤了。

下面就是USB安裝盤的格式：

* GUID Partition Map (GPT)
* 1 個分割區
* OS X Extended \(日誌式\) 檔案系統

首先，開啟找一台真的Mac電腦（找不到一台？沒關係，你可以參閱[這裡](extra\vmwaremac.md)，）。

先下載相應的macOS/Mac OS X的安裝檔：

macOS 10.14 Mojave：
```text
10.14.0: https://archive.syd.sh/macos/10.14.0.zip
```

macOS 10.13 High Sierra：
```text
10.13.6: https://archive.syd.sh/macos/10.13.6.zip
10.13.5: https://archive.syd.sh/macos/10.13.5.zip
10.13.4: https://archive.syd.sh/macos/10.13.4.zip
10.13.3: https://archive.syd.sh/macos/10.13.3.zip
10.13.2: https://archive.syd.sh/macos/10.13.2.zip
10.13.1: https://archive.syd.sh/macos/10.13.1.zip
10.13.0: https://archive.syd.sh/macos/10.13.0.zip
```

macOS 10.12 Sierra：
```text
10.12.6: https://archive.syd.sh/macos/10.12.6.zip
10.12.5: https://archive.syd.sh/macos/10.12.5.zip
10.12.4: https://archive.syd.sh/macos/10.12.4.zip
10.12.3: https://archive.syd.sh/macos/10.12.3.zip
10.12.2: https://archive.syd.sh/macos/10.12.2.zip
10.12.1: https://archive.syd.sh/macos/10.12.1.zip
10.12.0: https://archive.syd.sh/macos/10.12.0.zip
```

Mac OS X 10.11 El Captian：
```text
10.11.6: https://archive.syd.sh/macos/10.11.6.zip
```
你可以在以下的鏈接查看安裝檔的校驗值：
```text
https://archive.syd.sh/macos/SHA1SUMS
```

當下載完成後，就打開終端機， \(位於`/Launchpad/通用工具`\) 輸入 `diskutil list`.

你應該會看到所有已連接的內置/外置硬盤，以及它們的分區。搜尋一下你的USB是叫做disk#（#代表你的USB的數字）。 **要小心記下來，因為接下來的命令將會格式化你的USB!**

現在輸入以下的指令：

```text
diskutil partitionDisk /dev/disk#（#代表你的USB的數字） GPT JHFS+ "USB" 100%
```

現在，你的USB就會被命名成“USB”.

然後你就可以按照 [Apple 的教學](https://support.apple.com/zh-hk/HT201372)：

macOS 10.14 Mojave:
```text
sudo "/Applications/Install macOS Mojave.app/Contents/Resources/createinstallmedia" --volume /Volumes/USB
```

macOS 10.13 High Sierra:
```text
sudo /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/USB
```

macOS 10.12 Sierra:
```text
sudo /Applications/Install\ macOS\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/USB--applicationpath /Applications/Install\ macOS\ Sierra.app
```

Mac OS X 10.11 El Captian:
```text
sudo /Applications/Install\ OS\ X\ El\ Capitan.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume --applicationpath /Applications/Install\ OS\ X\ El\ Capitan.app
```

這個過程大概需要15～40分鐘，會因電腦的效能而有所差異。但是完成後，你的USB安裝盤就可以在任何一台真正的Mac電腦上安裝macOS/Mac OS X 了。但很顯然這不是我們的目標（否則你也不會按下這篇教學），因此在[下一步](gathering-kexts.md)中，去搜尋我們所需要的驅動程式(kexts)，並配置好Clover。

對了，這裡有個[懶人腳本](https://github.com/corpnewt/USB-Installer-Creator) ，它會自動化部署到USB的步驟。

