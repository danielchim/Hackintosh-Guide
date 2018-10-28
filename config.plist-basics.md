# Config.plist 入門

 _Config.plist_ 位於 _/你的EFI磁盤位置/EFI/CLOVER/config.plist_ 裡。這也是整個黑蘋果安裝中最具難度的一部分，因此我們現在來學習一下Config.plist的檔案結構，然後就根據不同的硬件平台去套用適當的配置檔。 

## 什麼是Config.plist?

 _Config.plist_ 是一個XML文件。 他和我們經常用來開發網頁的HTML類似，都是標記語言。
 也就是說，所有HTML要遵守的規範，在XML上也要遵守。

 ### 除錯
 為了防止新手設置錯Config.plist，其實你可以在macOS上輸入以下的指令來檢查你的Clover.plist有沒有標記錯誤：
 
 _plutil /你的/Config.plist/的路徑_

它可以幫你檢查你的Config.plist有沒有錯誤。


 ### 結構

當Clover打開 _config.plist_ 時，Clover會自動搜尋各個部件相應的描述的位置。因此Config.plist上所格式和編排的位置十分重要，否則Clover會跳過該段描述標記內容。 你可以在[Clover 維基](https://clover-wiki.zetam.org/Configuration#Config.plist-structure)上看到Config.plist應有的格式。

### 資料類型

在Config.plist中，我們會經常接觸到以下一些常見的資料類型：

#### 字串

例子： `<string>這是一個字串。</string>`

字串就是一些文字，你可以在一些註解或者其他的地方看到，沒有什麼特別。

#### 整數

例子： `<integer>1</integer>`

就是一些整數。

#### 數據

例子： `<data>RXh0ZXJuYWw=</data>`

儘管它看起來很像字串，但其實裡面儲存的是一些以base64表示的數據。這裡是維基百科關於base64的[傳送門](https://zh.wikipedia.org/wiki/Base64)。

 總括而言， 它可以將二進制檔案，以文本的方式儲存。這樣數據就不怕在移動，複製時被丟失。你也可以透過macOS的終端機，將base64的數據轉換成ASCII文字：
 
`echo RXh0ZXJuYWw= | python -m base64 -d && echo`

這行命令就會在下一行輸出 `External`。為了方便我們閱讀，我們可以使用 `&& echo` 指令來將輸出的文字放在下一行。

當然你也可以在終端機中，將ACSII文字轉換成base64數據：

`echo External | base64`

就會輸出`RXh0ZXJuYWw=`。 （這個功能在ACPi重命名時十分有用。）

**注意 -** 有些plist編輯器 \(例如 Clover Configurator, Xcode, 等等\) 會將base64顯示成 hexdecimal，因此要注意輸入。

#### 布爾值

例子： `<true/>` 或者 `<false/>`

這些就是布爾值，只有true和false。 **它們也是唯一集合開始和結束標記的標記語。**

#### 陣列

例子：
```markup
<array>
    <string>Bob</string>
    <string>Jim</string>
    <string>Chris</string>
</array>
```

在這個例子中，我們展示了一連串未經排序的資料。有時候如果我們想將一些名字組合在一起，我們可以像上面的例子一樣，在`<array>`裡使用`<string>`來儲存。它們可以通過以索引訪問 \(也就是它們的編號\).

#### 字典

例子：
```markup
<dict>
    <key>Name</key>
    <string>Bob</string>
    <key>Age</key>
    <integer>20</integer>
    <key>Knows XML</key>
    <true/>
</dict>
```

這裡展示了一個 `<dictionary>`的排序。它和 `<arrays>` 一樣，用作儲存一個組合的數據，但是`<dictionary>` 則利用 _key/value_ 來進行配對。和`<string>`一樣，裡面之儲存純文字。

### 示例

在這裡，我們透過一些例子，去解釋Config.plist文件的運作。

#### 切換 True/False 值

在這個例子裡，為了模擬現實情況，我們將會使用位於KextsToPatch (一個通知Clover要為在那裡打上kext補丁）的Disable值中，切換裡面的布爾值的`<ture>`到`<false>`，然後將其切換回來。

實例：
```markup
<dict>
    <key>Comment</key>
    <string>External icons patch</string>
    <key>Disabled</key>
    <false/>
    <key>Find</key>
    <data>
    RXh0ZXJuYWw=
    </data>
    <key>InfoPlistPatch</key>
    <false/>
    <key>Name</key>
    <string>AppleAHCIPort</string>
    <key>Replace</key>
    <data>
    SW50ZXJuYWw=
    </data>
</dict>
```

在當中：

* `Comment` - 就是一些注譯。

* `Disabled` - 這裡看起來好像有點違反直覺（電腦世界很多時候都是如此）， 當中的意思就是如果它的判斷值為`<ture>`的話，就不會打入該段補丁，Clover會跳過讀取它。但是設置成`<false>`的話，就**反而會打入該段補丁**。

* `InfoPlistPatch` - 這裡所使用的布爾值，會通知Clover我們會將補丁打入一個kext檔案裡的Info.plist文件，而不是kext裡包含的二進制檔案。

* `Name` - 通知Clover我們的會將補丁打入到哪一個kext裡。

* `Find` - 它會通知Clover我們希望在kext二進制檔案中，搜尋特定的base64數據的位置，以供作修改之用。

 
* `Replace` - 當我們找到了相應的數據時，`Replace`就會把相應的數據替換成我們想修改的數據檔案。

以上的例子中，我們透過以上的資料，Clover就會尋找 AppleAhHCIPort.kext，並在裡面搜尋base64數據`RXh0ZXJuYWw=`（也就是`External`的意思），然後將 `RXh0ZXJuYWw=` 替換成 `SW50ZXJuYWw=` （也就是`Internal`的意思）。

一切正常的話，出來的結果就是我們的黑蘋果現在可以支援熱插拔所有外置儲存裝置，並將其看成一個內置儲存裝置。

**注意：在當中我們所作的變更中，都沒有對硬盤/USB上的AppleAHCIPort.kext做出任何變更。**

好了，現在就來講一下如何將布爾值 `<ture>` 變成 `<false>`吧。我們這次將會修改 `Disabled` 的判斷值由 `<false>` 改成 `<ture>`：

```markup
<dict>
    <key>Comment</key>
    <string>External icons patch</string>
    <key>Disabled</key>
    <true/>
    <key>Find</key>
    <data>
    RXh0ZXJuYWw=
    </data>
    <key>InfoPlistPatch</key>
    <false/>
    <key>Name</key>
    <string>AppleAHCIPort</string>
    <key>Replace</key>
    <data>
    SW50ZXJuYWw=
    </data>
</dict>
```

就是這麼簡單。

#### 新增一個字典 (`<dict>`)到一個陣列 (`<array>`) 

這可能對於新手而言有點難度，在這裡，你需要將一個補丁打入到  _config.plist -&gt; ACPI -&gt; DSDT -&gt; Patches_ 裡。我們先打開Config.plist:


```markup
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>ACPI</key>
    <dict>
        <key>DSDT</key>
        <dict>
            <key>Fixes</key>
            <dict>
                <key>FixHPET</key>
                <true/>
                <key>FixIPIC</key>
                <true/>
                <key>FixRTC</key>
                <true/>
                <key>FixTMR</key>
                <true/>
            </dict>
            <key>Patches</key>
            <array>
                <dict>
                    <key>Comment</key>
                    <string>change OSID to XSID (to avoid match against _OSI XOSI patch)</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    T1NJRA==
                    </data>
                    <key>Replace</key>
                    <data>
                    WFNJRA==
                    </data>
                </dict>
                <dict>
                    <key>Comment</key>
                    <string>change _OSI to XOSI</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    X09TSQ==
                    </data>
                    <key>Replace</key>
                    <data>
                    WE9TSQ==
                    </data>
                </dict>
            </array>
```

在裡面,我們先看到在 ACPI下的 DSDT，而在DSDT下的就是 Fixes。 然後在Fixes下面的就是 _Patches_。我們現在先不用管Fixes區域，只需要專注在Patches就可以了。

首先
Firstly, I'll point out that under the `<key>Patches</key>` is an opening array tag \(`<array>`\) - and then we have 2 dictionaries - each with similar keys to what we worked with in the prior example \(_Comment_, _Disabled_, _Find_, _Replace_\). After the dictionaries, we see the closing array tag \(`</array>`\). Our goal is to add a new dictionary in between the `<array>` and `</array>` tags while also avoiding slicing up the other existing dictionaries. The data that we'll be adding looks like so:

```markup
<dict>
    <key>Comment</key>
    <string>change SAT0 to SATA</string>
    <key>Disabled</key>
    <false/>
    <key>Find</key>
    <data>
    U0FUMA==
    </data>
    <key>Replace</key>
    <data>
    U0FUQQ==
    </data>
</dict>
```

Like I mentioned prior, arrays are unordered - that means it doesn't matter whether we put our new dictionary before the existing 2, after them, or in between them. I'm going to add it to the end though - just above that last `</array>` tag like so:

```markup
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>ACPI</key>
    <dict>
        <key>DSDT</key>
        <dict>
            <key>Fixes</key>
            <dict>
                <key>FixHPET</key>
                <true/>
                <key>FixIPIC</key>
                <true/>
                <key>FixRTC</key>
                <true/>
                <key>FixTMR</key>
                <true/>
            </dict>
            <key>Patches</key>
            <array>
                <dict>
                    <key>Comment</key>
                    <string>change OSID to XSID (to avoid match against _OSI XOSI patch)</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    T1NJRA==
                    </data>
                    <key>Replace</key>
                    <data>
                    WFNJRA==
                    </data>
                </dict>
                <dict>
                    <key>Comment</key>
                    <string>change _OSI to XOSI</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    X09TSQ==
                    </data>
                    <key>Replace</key>
                    <data>
                    WE9TSQ==
                    </data>
                </dict>
                <dict>
                    <key>Comment</key>
                    <string>change SAT0 to SATA</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    U0FUMA==
                    </data>
                    <key>Replace</key>
                    <data>
                    U0FUQQ==
                    </data>
                </dict>
            </array>
```

#### 更多示例

計劃會再加入更多的plist的示例。

