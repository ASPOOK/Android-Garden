# Android-Garden

#### Android知识点备忘、开发技巧、踩过的坑……没有顺序，不分主次，遇到即写，想到即写，杂而不乱，如群芳争艳的花园。

1.APK是Android安装包，为Android Package的缩写。

2.每个应用启动时都是在各自独立的进程里，每个进程都有自己的虚拟机。

3.AAPT(Android Asset Packaging Tool)会把除/res/raw/目录之外的资源文件编译成二进制格式，包括xml文件；/res/raw/中的文件是资源，可以通过资源id来访问，如R.raw.xxx，但/assets/文件夹下的内容并不算真正意义上的资源，里面的文件不会生成资源id，它支持任意子目录和任意文件，且必须通过文件路径来读取。

4.
