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

15.


