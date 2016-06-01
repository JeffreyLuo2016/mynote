<h3 align="center">iOS9 HTTP 不能正常使用的解决办法</h3>

####升级到Xcode7 写的demo中访问网络失败 出现如下提示:
>The resource could not be loaded because the App Transport Security policy requires the use of a secure connection.

经查是因为IOS9中引入了新特性==App Transport Security (ATS)==,详情：[App Transport Security (ATS)](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html#//apple_ref/doc/uid/TP40016198-DontLinkElementID_13)   

新特性要求App内访问的网络必须使用==HTTPS==协议。
但是现在公司的项目使用的是==HTTP==协议，使用私有加密方式保证数据安全。现在也==***不能马上***==改成HTTPS协议传输。

### ***解决办法***：
1. 在Info.plist中添加==*NSAppTransportSecurity*==类型==*Dictionary*==。
2. 在*==NSAppTransportSecurity==*下添加*==NSAllowsArbitraryLoads==*类型==*Boolean*==,值设为*==YES==*。