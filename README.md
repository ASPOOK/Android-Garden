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

8.关于AsyncTask,在1.6之前是在单线程上串行执行的；从1.6开始改为在多线程并行执行；坑爹的是从3.0开始又改回在单线程上串行执行。<br/>
常用方式如下，以达到多线程并行的目的：<br/>
ExecutorService executor  = (ExecutorService) Executors.newFixedThreadPool(10);<br/>
task.executeOnExecutor(executor, params);

9.APK反编译后在res文件夹中找不到values文件夹，是因为在打包APK时,res/values目录下的资源文件内容经过编译之后直接写入到资源索引表中（resources.arsc）了

10.在一些自定义控件中，如果只能监听到MotionEvent.ACTION_DOWN,而监听不到MotionEvent.ACTION_MOVE和MotionEvent.ACTION_UP,则需要在代码中设置setClickable(true)，或者在布局文件里设置属性：android:clickable="true"。通过给view设置setOnClickListener(OnClickListener l)也可以，其内部会自动调用setClickable(true),setOnLongClickListener同理。

11.Java虚拟机是基于栈（stack-based）的，而Dalvik是基于寄存器(register-based)的，速度相对会更快一些。

12.只有在同一个task中的activity才能使用startActivityForResult()传递数据

13.当一个APK被安装到手机时，会将文件放在data/data/<package-name>目录下，实质上就是ZIP的压缩包，之后ART会将其中的dex文件编译成native library，放置在data/dalvik-cache目录下，再次启动App时不会重新编译dex，而是直接加载第一次生成的native library，因此启动速度会加快。

14.(1)关于Activity的onSavaInstanceState()回调（摘自[Android Developers](https://developer.android.com/guide/components/activities.html)）——There's no guarantee that onSaveInstanceState() will be called before your activity is destroyed, because there are cases in which it won't be necessary to save the state (such as when the user leaves your activity using the Back button, because the user is explicitly closing the activity). If the system calls onSaveInstanceState(), it does so before onStop() and possibly before onPause().

(2)However, even if you do nothing and do not implement onSaveInstanceState(), some of the activity state is restored by the Activity class's default implementation of onSaveInstanceState(). Specifically, the default implementation calls the corresponding onSaveInstanceState() method for every View in the layout, which allows each view to provide information about itself that should be saved. Almost every widget in the Android framework implements this method as appropriate, such that any visible changes to the UI are automatically saved and restored when your activity is recreated. For example, the EditText widget saves any text entered by the user and the CheckBox widget saves whether it's checked or not. **The only work required of you is to provide a unique ID (with the android:id attribute) for each widget you want to save its state. If a widget does not have an ID, then the system cannot save its state.**

(3)Because onSaveInstanceState() is not guaranteed to be called, you should use it only to record the transient state of the activity (the state of the UI)—you should never use it to store persistent data. Instead, you should use onPause() to store persistent data (such as data that should be saved to a database) when the user leaves the activity.

(4）当Activity A启动Activity B时的生命周期：

<1>Activity A's onPause() method executes.

<2>Activity B's onCreate(), onStart(), and onResume() methods execute in sequence. (Activity B now has user focus.)

<3>Then, if Activity A is no longer visible on screen, its onStop() method executes.

This predictable sequence of lifecycle callbacks allows you to manage the transition of information from one activity to another. For example, **if you must write to a database when the first activity stops so that the following activity can read it, then you should write to the database during onPause() instead of during onStop().**

15.Handling Runtime Changes

- Retain an object during a configuration change

使用Framgment保存数据，在Fragment的onCreate中调用setRetainInstance(true)，当发生配置改变，Activity销毁时利用Fragment保存数据，当Activity重建后，从Fragment中恢复数据。

- Handle the configuration change yourself

利用如下配置阻止Activity重建

    <activity android:name=".MyActivity"
        android:configChanges="orientation|keyboardHidden|screenSize"
        android:label="@string/app_name">

并响应 onConfigurationChanged()回调，做一些需要的逻辑：

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
    
        // Checks the orientation of the screen
        if (newConfig.orientation == Configuration.ORIENTATION_LANDSCAPE) {
            Toast.makeText(this, "landscape", Toast.LENGTH_SHORT).show();
        } else if (newConfig.orientation == Configuration.ORIENTATION_PORTRAIT){
            Toast.makeText(this, "portrait", Toast.LENGTH_SHORT).show();
        }
    }

注意参数里面的Configuration是当前的状态配置，而不是改变前的配置。

参考：[Handling Runtime Changes](https://developer.android.com/guide/topics/resources/runtime-changes.html#RetainingAnObject)

16.Android系统在启动过程中，会扫描系统中的特定目录，以便可以对保存在里面的应用程序进行安装，这是通过Package管理服务PackageManagerService来实现的。Package管理服务在安装一个应用程序的时候，主要做两件事情，第一是解析这个应用程序的配置文件AndroidManifest.xml，以便获得它的安装信息；第二是为这个应用分配一个Linux用户ID和Linux用户组。PackageManagerService在安装一个应用程序时，如果发现它申请了一个特定的资源访问权限，那么就会为它分配一个相应的Linux用户组ID。

Android系统每次启动时，都需重新安装一遍系统中的应用程序，但是有些应用程序信息每次安装都是需要保持一致的，例如应用程序的Linux用户ID，否则应用程序每次在系统重新启动后表现可能不一致。因此PackageManagerService在每次安装完成应用程序之后，都需要将它们的信息保存下来，以便下次安装时可以恢复回来。

系统中所有已安装了的应用程序都是使用一个Package对象来描述的，这些Package对象保存在PackageManagerServices类的成员变量mPackages所描述的一个HashMap中，并且以这些Package对象所描述的应用的包名作为关键字。

Android系统启动过程的最后一步是启动一个Home应用程序，用来显示系统中已经安装了应用程序。Android系统提供了一个默认的Launcher应用，还可以自己定义Launcher。Launcher启动过程中，首先会请求Package管理服务PackageManagerService返回系统中已安装了的应用程序的信息，接着分别将这些应用程序信息封装成一个快捷图标显示在系统屏幕上，以便用户可以通过点击这些快捷图标来启动相应的应用程序。

17.代码混淆导致属性动画失效

现象：对某个view使用属性动画，自定义了一个包装类ViewWrapper，并提供了针对某一属性的get、set方法，在测试时正常，待签名后安装则属性动画无效了。

原因：此为代码混淆将自定义包装类混淆了，导致属性动画找不到对应的属性，因此属性动画失效。

解决方案：避免混淆该包装类。

18.Mac上Android Studio使用adb命令

adb环境变量设置：

（1）在Terminal输入以下命令：open -e .bash_profile

（2)回车打开 .bash_profile文件，之前没配置过应该是空白的，输入export PATH=${PATH}:~/Library/Android/sdk/tools:~/Library/Android/sdk/platform-tools，这两个路径是tools和platform-tools在个人电脑上的位置，根据自己电脑SDK下tools和platform-tools的路径替换下，保存。

（3）再输adb shell就ok了

19.Android WebView打开http与https混用的网页
需要使用如下方法:

WebSettings.setMixedContentMode(int mode)  **Added in API level 21**

    void setMixedContentMode (int mode)

Configures the WebView's behavior when a secure origin attempts to load a resource from an insecure origin. By default, apps that target KITKAT or below default to MIXED_CONTENT_ALWAYS_ALLOW. Apps targeting LOLLIPOP default to MIXED_CONTENT_NEVER_ALLOW. The preferred and most secure mode of operation for the WebView is MIXED_CONTENT_NEVER_ALLOW and use of MIXED_CONTENT_ALWAYS_ALLOW is strongly discouraged.

Parameters
mode	int: The mixed content mode to use. One of `MIXED_CONTENT_NEVER_ALLOW`, `MIXED_CONTENT_ALWAYS_ALLOW` or `MIXED_CONTENT_COMPATIBILITY_MODE`.

