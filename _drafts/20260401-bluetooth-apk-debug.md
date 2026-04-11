# 反编译apk
JADX-GUI： 用于静态分析，查看反编译后的 Java 伪代码，寻找登录逻辑所在的 Activity。只能查看，不能修改。需要安装jre
![](images/posts/20260402-1.jpeg)
apk-editor-studio：可以修改并保存apk
![](images/posts/20260402.jpeg)
Apktool： 用于反编译资源文件和 Smali 代码，以及回编译修改后的 APK。
MT 管理器 (Android 端)： 这是一个比较“硬核”的手机端工具，支持直接在手机上修改 Smali 和签名，门槛相对较低。
## 现实的阻碍
代码混淆： 现代 App 几乎都会使用 ProGuard 或 R8 进行代码混淆，你看到的函数名可能是 a, b, c，极难分辨哪个是登录函数。
数据完整性： 很多健康类 App 会对本地数据库进行加密，密钥通常是在登录后从服务器下发的。如果没有登录，你可能连本地旧数据都打不开。
# 蓝牙调试工具
## nRF Connect for Mobile（开发者常用的蓝牙调试工具）。
开启血压计的蓝牙（通常是长按“开始”键或“记忆”键，直到蓝牙图标闪烁）。
在 nRF Connect 中点击 Scan，找到你的设备并点击 Connect。
![](images/posts/20260402-2.jpeg)
查看服务列表（Services）：
![](images/posts/20260402-3.jpeg)
如果你看到一个 UUID 为 0x1810 的服务，恭喜你，它使用的是国际标准血压协议。
如果你只看到 0xFF01 之类的自定义 UUID，说明它是私有协议，通用 App 可能无法直接读取。此时可以通过发送指令来操作硬件
![](images/posts/20260402-4.jpeg)
# 血压计开源app
MedM Blood Pressure (推荐等级：⭐⭐⭐⭐⭐)
这是目前全球兼容性最强的血压管理 App。
做法： 打开 App -> 设置 -> 增加设备。
优点： 它内置了数百种协议。即使不是标准协议，只要是市面上常见的方案，它都能识别。