# Android-Garden

#### Android知识点备忘、开发技巧、踩过的坑……没有顺序，不分主次，遇到即写，想到即写，杂而不乱，如群芳争艳的花园。

1.APK是Android安装包，为Android Package的缩写。

2.每个应用启动时都是在各自独立的进程里，每个进程都有自己的虚拟机。

3.AAPT(Android Asset Packaging Tool)会把除/res/raw/目录之外的资源文件编译成二进制格式，包括xml文件；/res/raw/中的文件是资源，可以通过资源id来访问，如R.raw.xxx，但/assets/文件夹下的内容并不算真正意义上的资源，里面的文件不会生成资源id，它支持任意子目录和任意文件，且必须通过文件路径来读取。

4.Android M新的权限模式（译文）—— [探索新的Android权限模式](http://blog.csdn.net/ahence/article/details/48156485)

5.关于Android文件存储及读取权限（译文）—— [Android文件存储](http://blog.csdn.net/ahence/article/details/47659263)

6.Android签名证书的有效期尽可能设置的长一些，建议至少25年；事实上Android仅仅在安装时检测证书的有效期，如果证书到期了，应用程序可以继续运行，只是无法再更新应用程序，唯一的选择是创建另一个具有不同包名的应用。关于如何生存签名证书，请参考 [Android生成keystore](http://blog.csdn.net/ahence/article/details/26583611)

7.关于Fragment的构造函数<br/>
先附一段来自官方文档的说明：Every fragment must have an empty constructor, so it can be instantiated when restoring its activity's state. It is strongly recommended that subclasses do not have other constructors with parameters, since these constructors will not be called when the fragment is re-instantiated; instead, arguments can be supplied by the caller with setArguments(Bundle) and later retrieved by the Fragment with getArguments().<br/>
大意是说尽可能不要为Fragment创建带参数的构造函数，因为在重新实例化的时候不一定会执行到该函数；如果需要传值建议使用setArguments(Bundle bundle)，之后在Fragment内通过getArguments()获取数据。<br/>
此外还有另一种常用创建Fragment实例的方法，即为Fragment定义一个newInstance()方法，该方法可以附带参数。