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

儘管它看起來很像字串，但其實裡面儲存的是一些以base64表示的數據。這裡是維基百科關於base64的[傳送門](https://zh.wikipedia.org/wiki/Base64), 總括而言， 它可以將二進制檔案，以文本的方式儲存。這樣數據就不怕在移動，複製時被丟失。你也可以透過macOS的終端機，將base64的數據轉換成ASCII文字：
 
`echo RXh0ZXJuYWw= | python -m base64 -d && echo`

這行命令就會在下一行輸出 `External`。為了方便我們閱讀，我們可以使用 `&& echo` 指令來將輸出的文字放在下一行。

當然你也可以在終端機中，將ACSII文字轉換成base64數據：
You can also convert from ASCII to base64 \(handy for working with ACPI renames - more about that later\) with the following in Terminal.app:

`echo External | base64`

就會輸出`RXh0ZXJuYWw=`，這個功能在ACPi重命名時十分有用。

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

這是一連串未經排序的資料。有時候如果我們想將一些名字組合在一起，我們可以像上面的例子一樣，在`<array>`裡使用`<string>`來儲存。它們可以通過以索引訪問 \(也就是他們的編號\).

#### 字典

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

This denotes a _dictionary_. These, like _arrays_ are great for storing extra collections of data, but instead of being index based, they utilize _key/value_ organization. As you can see from the above example, we are able to store specific data about _Bob_ through the use of these _key/value_ pairs. All the keys are just text \(like our strings\).

### Examples

Let's go over some _before/after_ examples with some pretend _config.plist_ data to hopefully remove some of the mystery that happens in this wizard's closet.

#### Change True/False

In this first example, we're just going to change a boolean value from true to false, or vise versa. I'll use the _Disabled_ value inside a _KextsToPatch_ entry to make this actually a real-world example. First, I'll give us the _KextsToPatch_ entry we'll be working with:

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

Whew, that might look like a lot up front, but we'll break things down. Firstly, I'll go over what the different keys mean:

* `Comment` - this is just a comment to describe what the patch is doing.
* `Disabled` - this is a bit counter-intuitive, but it's a boolean value that determines whether or not this patch is disabled.  If set to `<true/>`, the patch will be disabled, and Clover will ignore it.  If set to `<false/>` the patch is _not_ disabled, and it will be applied.
* `InfoPlistPatch` - this is a boolean value that tells Clover if we're patching the Info.plist of the kext instead of the binary.
* `Name` - this is the actual kext we intend to patch.
* `Find` - this is the base64 data we want to look for in the binary to patch.
* `Replace` - this is what we will be replacing the `Find` data with \(if we find it\).

Alright, now I'll explain what this patch is actually for. Via the information in this patch, Clover will look for the _AppleAHCIPort_ kext and search for `RXh0ZXJuYWw=` \(which becomes `External` when we decode the data\) and replace it with `SW50ZXJuYWw=` \(which becomes `Internal` when we decode it\). The end result is that drives that are hot-pluggable \(and normally considered external drives\) will be displayed as internal drives and not have the orange icon on the desktop. This patching happens on the fly, and is non-destructive - meaning that the _AppleAHCIPort_ kext remains untouched on the system.

So - I guess at this point, I should explain how we would change a boolean value to disable this patch. I mentioned before how the `Disabled` key works - wo we'll change the `<false/>` on the next line to `<true/>` which sets this patch to _disabled_ like so:

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

Not too scary, right?

#### Adding a New Dict to an Array

This is one that I see quite often that can be a bit overwhelming for new folks. If you are told to add a new patch to _config.plist -&gt; ACPI -&gt; DSDT -&gt; Patches_, we'd first pop open our _config.plist_ and see what we're working with.

We'll assume for this example that the config looks like so:

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

As we look down the config, starting at the top, we can follow that path I outlined before. We see _ACPI_, and under that _DSDT_. Then underneath _DSDT_ is _Fixes_ and in-line with that is _Patches_. We're not concerned with the _Fixes_ section currently, so we'll just ignore that and focus on the _Patches_.

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

#### More Examples

I'll try to keep my ears open for more plist editing examples that people have issues with, and add them as needed.

