# BrainTree Drop-In startup crash repro

This is minimum reproducible sample for crash that happens at app startup with latest BrainTree Drop-In SDK and AGP 9 only on minified builds.

- Install and run `debug` build and observe no crash
- Install and run `release` build type and observe crash at startup

```
FATAL EXCEPTION: main
Process: dev.matsem.braintreerepro, PID: 26660
java.lang.RuntimeException: Unable to get provider androidx.startup.InitializationProvider: c8.m: java.lang.RuntimeException: Failed to create an instance of androidx.work.impl.WorkDatabase
	at android.app.ActivityThread.installProvider(ActivityThread.java:7493)
	at android.app.ActivityThread.installContentProviders(ActivityThread.java:6999)
	at android.app.ActivityThread.handleBindApplication(ActivityThread.java:6770)
	at android.app.ActivityThread.-$$Nest$mhandleBindApplication(Unknown Source:0)
	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2134)
	at android.os.Handler.dispatchMessage(Handler.java:106)
	at android.os.Looper.loopOnce(Looper.java:201)
	at android.os.Looper.loop(Looper.java:288)
	at android.app.ActivityThread.main(ActivityThread.java:7898)
	at java.lang.reflect.Method.invoke(Native Method)
	at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:548)
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:936)
Caused by: c8.m: java.lang.RuntimeException: Failed to create an instance of androidx.work.impl.WorkDatabase
	at d4.a.b(SourceFile:96)
	at d4.a.a(SourceFile:82)
	at androidx.startup.InitializationProvider.onCreate(SourceFile:51)
	at android.content.ContentProvider.attachInfo(ContentProvider.java:2451)
	at android.content.ContentProvider.attachInfo(ContentProvider.java:2421)
	at android.app.ActivityThread.installProvider(ActivityThread.java:7488)
	at android.app.ActivityThread.installContentProviders(ActivityThread.java:6999) 
	at android.app.ActivityThread.handleBindApplication(ActivityThread.java:6770) 
	at android.app.ActivityThread.-$$Nest$mhandleBindApplication(Unknown Source:0) 
	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2134) 
	at android.os.Handler.dispatchMessage(Handler.java:106) 
	at android.os.Looper.loopOnce(Looper.java:201) 
	at android.os.Looper.loop(Looper.java:288) 
	at android.app.ActivityThread.main(ActivityThread.java:7898) 
	at java.lang.reflect.Method.invoke(Native Method) 
	at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:548) 
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:936) 
Caused by: java.lang.RuntimeException: Failed to create an instance of androidx.work.impl.WorkDatabase
	at v3.i.b(SourceFile:606)
	at k4.m.<init>(SourceFile:200)
	at androidx.work.WorkManagerInitializer.b(SourceFile:121)
	at d4.a.b(SourceFile:82)
	at d4.a.a(SourceFile:82) 
	at androidx.startup.InitializationProvider.onCreate(SourceFile:51) 
	at android.content.ContentProvider.attachInfo(ContentProvider.java:2451) 
	at android.content.ContentProvider.attachInfo(ContentProvider.java:2421) 
	at android.app.ActivityThread.installProvider(ActivityThread.java:7488) 
	at android.app.ActivityThread.installContentProviders(ActivityThread.java:6999) 
	at android.app.ActivityThread.handleBindApplication(ActivityThread.java:6770) 
	at android.app.ActivityThread.-$$Nest$mhandleBindApplication(Unknown Source:0) 
	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2134) 
	at android.os.Handler.dispatchMessage(Handler.java:106) 
	at android.os.Looper.loopOnce(Looper.java:201) 
	at android.os.Looper.loop(Looper.java:288) 
	at android.app.ActivityThread.main(ActivityThread.java:7898) 
	at java.lang.reflect.Method.invoke(Native Method) 
	at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:548) 
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:936) 
```

Disabling `androidx.startup.InitializationProvider` inside Manifest reveals issues with creation of analytics Room database

```xml
<provider
    android:name="androidx.startup.InitializationProvider"
    android:authorities="${applicationId}.androidx-startup"
    tools:node="remove" />
```

```
FATAL EXCEPTION: main
Process: dev.matsem.braintreerepro, PID: 28516
java.lang.RuntimeException: Unable to start activity ComponentInfo{dev.matsem.braintreerepro/dev.matsem.braintreerepro.MainActivity}: java.lang.RuntimeException: Failed to create an instance of com.braintreepayments.api.AnalyticsDatabase
	at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:3676)
	at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:3813)
	at android.app.servertransaction.LaunchActivityItem.execute(LaunchActivityItem.java:101)
	at android.app.servertransaction.TransactionExecutor.executeCallbacks(TransactionExecutor.java:135)
	at android.app.servertransaction.TransactionExecutor.execute(TransactionExecutor.java:95)
	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2308)
	at android.os.Handler.dispatchMessage(Handler.java:106)
	at android.os.Looper.loopOnce(Looper.java:201)
	at android.os.Looper.loop(Looper.java:288)
	at android.app.ActivityThread.main(ActivityThread.java:7898)
	at java.lang.reflect.Method.invoke(Native Method)
	at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:548)
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:936)
Caused by: java.lang.RuntimeException: Failed to create an instance of com.braintreepayments.api.AnalyticsDatabase
	at h1.i.a(SourceFile:700)
	at a2.t0.<init>(SourceFile:161)
	at dev.matsem.braintreerepro.MainActivity.onCreate(SourceFile:23)
	at android.app.Activity.performCreate(Activity.java:8290)
	at android.app.Activity.performCreate(Activity.java:8269)
	at android.app.Instrumentation.callActivityOnCreate(Instrumentation.java:1384)
	at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:3657)
	at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:3813) 
	at android.app.servertransaction.LaunchActivityItem.execute(LaunchActivityItem.java:101) 
	at android.app.servertransaction.TransactionExecutor.executeCallbacks(TransactionExecutor.java:135) 
	at android.app.servertransaction.TransactionExecutor.execute(TransactionExecutor.java:95) 
	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2308) 
	at android.os.Handler.dispatchMessage(Handler.java:106) 
	at android.os.Looper.loopOnce(Looper.java:201) 
	at android.os.Looper.loop(Looper.java:288) 
	at android.app.ActivityThread.main(ActivityThread.java:7898) 
	at java.lang.reflect.Method.invoke(Native Method) 
	at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:548) 
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:936) 
```
