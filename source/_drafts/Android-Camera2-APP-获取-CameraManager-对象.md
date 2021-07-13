---

title: Android Camera2  APP 获取 CameraManager 对象
date: 2021-06-07 11:27:35
tags:
- Camera
- Android
categories:
- OS
---

<p style="text-indent:2em">对于一般人而言，Camera App 是直接跟他们交互的部分。Camera App 通过对 Framework API2 的调用，完成整个 Camera 的功能。其中 CameraManager 是一切开始的地方，CameraManager类是开放给 App 使用相机的入口类，它包括了获取相机 ID 列表、打开和关闭相机等操作，用于检测、表征和连接到 CameraDevice，它可以通过获取系统服务的方式被拿到。下面是 App 获取CameraManager 类的代码。</p>

```java
val manager = activity.getSystemService(Context.CAMERA_SERVICE) as CameraManager 
//or 
CameraManager manager = (CameraManager) activity.getSystemService(Context.CAMERA_SERVICE); 
```

<p style="text-indent:2em">这里传入的是Context.CAMERA_SERVICE，CAMERA_SERVICE 定义在 Context 中：CAMERA_SERVICE = “camera”。Context#getSystemService方法根据传入的 name 来取得对应的Object，然后转换成相应的服务对象。</p>

```java
// frameworks/base/core/java/android/content/Context.java

public abstract @Nullable Object getSystemService(@ServiceName @NonNull String name);

public static final String CAMERA_SERVICE="camera";
```

<p style="text-indent:2em">Android的后台运行着很多Service，它们在系统启动时被SystemServer开启，支持系统的正常工作，相应的名字会注册到SystemService中，当外部需要用到这些服务时，就会通过getSystemService(Context.name)获取对应的服务。</p>

| 传入的Name              | 返回的对象          | 说明                   |
| ----------------------- | ------------------- | ---------------------- |
| WINDOW_SERVICE          | WindowManager       | 管理打开的窗口程序     |
| LAYOUT_INFLATER_SERVICE | LayoutInflater      | 取得xml里定义的view    |
| ACTIVITY_SERVICE        | ActivityManager     | 管理应用程序的系统状态 |
| POWER_SERVICE           | PowerManger         | 电源的服务             |
| ALARM_SERVICE           | AlarmManager        | 闹钟的服务             |
| NOTIFICATION_SERVICE    | NotificationManager | 状态栏的服务           |
| KEYGUARD_SERVICE        | KeyguardManager     | 键盘锁的服务           |
| LOCATION_SERVICE        | LocationManager     | 位置的服务，如GPS      |
| SEARCH_SERVICE          | SearchManager       | 搜索的服务             |
| VEBRATOR_SERVICE        | Vebrator            | 手机震动的服务         |
| CONNECTIVITY_SERVICE    | Connectivity        | 网络连接的服务         |
| WIFI_SERVICE            | WifiManager         | Wi-Fi服务              |
| TELEPHONY_SERVICE       | TeleponyManager     | 电话服务               |

<p style="text-indent:2em">由于传入的 name 既不等于 WINDOW_SERVICE，也不等于 SEARCH_SERVICE，因此会调用其父类 ContextThemeWrapper 的 getSystemService 查找。</p>

```java
// frameworks/base/core/java/android/app/Activity.java

public class Activity extends ContextThemeWrapper
        implements LayoutInflater.Factory2,
        Window.Callback, KeyEvent.Callback,
        OnCreateContextMenuListener, ComponentCallbacks2,
        Window.OnWindowDismissedCallback,
        AutofillManager.AutofillClient, ContentCaptureManager.ContentCaptureClient {    
    ......
    @Override
    public Object getSystemService(@ServiceName @NonNull String name) {
        if (getBaseContext() == null) {
            throw new IllegalStateException(
                    "System services not available to Activities before onCreate()");
        }

        if (WINDOW_SERVICE.equals(name)) {
            return mWindowManager;
        } else if (SEARCH_SERVICE.equals(name)) {
            ensureSearchManager();
            return mSearchManager;
        }
        return super.getSystemService(name);
    }
    ......
}

// frameworks/base/core/java/android/view/ContextThemeWrapper.java
 public class ContextThemeWrapper extends ContextWrapper {
     ......
    @Override
    public Object getSystemService(String name) {
        if (LAYOUT_INFLATER_SERVICE.equals(name)) {
            if (mInflater == null) {
                mInflater = LayoutInflater.from(getBaseContext()).cloneInContext(this);
            }
            return mInflater;
        }
        return getBaseContext().getSystemService(name);
    }
} 

// frameworks/base/core/java/android/content/ContextWrapper.java
public class ContextWrapper extends Context {
    @UnsupportedAppUsage
    Context mBase;

    public ContextWrapper(Context base) {
        mBase = base;
    }
    public Context getBaseContext() {
        return mBase;
    }
}
```

<p style="text-indent:2em">结合源码分析,不难知道最终会调用 ContextImpl 内的方法 getSystemService()。 </p>

```java
// frameworks/base/core/java/android/app/ContextImpl.java

class ContextImpl extends Context {
    ......
    public Object getSystemService(String name) {
        ......
        return SystemServiceRegistry.getSystemService(this, name);
    }
    ......
}
```

<p style="text-indent:2em">SystemServiceRegistry 类管理所有可以由 Context#getSystemService 返回的系统服务。SYSTEM_SERVICE_FETCHERS 是一个 ArrayMap，其 key 类型为 String，value 是 ServiceFetcher&lt?&gt。那么这个 ArrayMap 在哪里填充呢？</p>

```java
// frameworks/base/core/java/android/app/SystemServiceRegistry.java

final class SystemServiceRegistry {
    ......
    private static final Map<String, ServiceFetcher<?>> SYSTEM_SERVICE_FETCHERS =
            new ArrayMap<String, ServiceFetcher<?>>();  
    ......
    public static Object getSystemService(ContextImpl ctx, String name)
         ......
        final ServiceFetcher<?> fetcher = SYSTEM_SERVICE_FETCHERS.get(name);
        ......
        final Object ret = fetcher.getService(ctx);
        ......
        return ret;
    }    
    ......
}
```

<p style="text-indent:2em">原来是在 SystemServiceRegistry 类静态块中添加的，其中就包括了 CAMERA_SERVICE。CachedServiceFetcher 继承自 ServiceFetcher。CachedServiceFetcher 是一个抽象类，其 createService(…) 方法待实现(在SystemServiceRegistry 类静态块中添加CAMERA_SERVICE实例化具体的CachedServiceFetcher&ltCameraManager&gt类时实现createService(…) 方法)。其 getService(…) 方法首先从 ContextImpl 成员变量 mServiceCache 获取 cache 数组，接着从对应的 mCacheIndex 拿到 Object，第一次获取由于这个“坑位”在 CachedServiceFetcher 构造函数中刚增加(mCacheIndex = sServiceCacheSize++)，所以 service 必然为 null，接着就会调用 createService(…) 创建相应的对象，并给对应的缓存“坑位”赋值。</p>

```java
//frameworks/base/core/java/android/app/SystemServiceRegistry.java

final class SystemServiceRegistry {
    ......
    private static int sServiceCacheSize;
    ......
    static {
        ......
        registerService(Context.CAMERA_SERVICE, CameraManager.class,
                new CachedServiceFetcher<CameraManager>() {
            @Override
            public CameraManager createService(ContextImpl ctx) {
                return new CameraManager(ctx);
            }});       
        ......
    }
    ......
    private static <T> void registerService(@NonNull String serviceName,
            @NonNull Class<T> serviceClass, @NonNull ServiceFetcher<T> serviceFetcher) {
        SYSTEM_SERVICE_NAMES.put(serviceClass, serviceName);
        SYSTEM_SERVICE_FETCHERS.put(serviceName, serviceFetcher);
        SYSTEM_SERVICE_CLASS_NAMES.put(serviceName, serviceClass.getSimpleName());
    }
    ......
    static abstract class CachedServiceFetcher<T> implements ServiceFetcher<T> {
        private final int mCacheIndex;

        CachedServiceFetcher() {
            mCacheIndex = sServiceCacheSize++;
        }

        @Override
        @SuppressWarnings("unchecked")
        public final T getService(ContextImpl ctx) {
            final Object[] cache = ctx.mServiceCache
            final int[] gates = ctx.mServiceInitializationStateArray;
            boolean interrupted = false;
            
            for (;;) {
                boolean doInitialize = false;
                synchronized (cache) {
                    T service = (T) cache[mCacheIndex];
                    if (service != null || gates[mCacheIndex] == ContextImpl.STATE_NOT_FOUND) {
                        ret = service;
                        break; // exit the for (;;)

                        // If we get here, there's no cached instance.

                        // Grr... if gate is STATE_READY, then this means we initialized the service
                        // once but someone cleared it.
                        // We start over from STATE_UNINITIALIZED.
                        if (gates[mCacheIndex] == ContextImpl.STATE_READY) {
                           gates[mCacheIndex] = ContextImpl.STATE_UNINITIALIZED;
                        }

                        // It's possible for multiple threads to get here at the same time, so
                        // use the "gate" to make sure only the first thread will call createService().

                        // At this point, the gate must be either UNINITIALIZED or INITIALIZING.
                        if (gates[mCacheIndex] == ContextImpl.STATE_UNINITIALIZED) {
                            doInitialize = true;
                            gates[mCacheIndex] = ContextImpl.STATE_INITIALIZING;
                        }
                    }
                    
                    if (doInitialize) {
                        // Only the first thread gets here.

                        T service = null;
                        @ServiceInitializationState int newState = ContextImpl.STATE_NOT_FOUND;
                        try {
                            // This thread is the first one to get here. Instantiate the service
                            // *without* the cache lock held.
                            service = createService(ctx);
                            newState = ContextImpl.STATE_READY;

                        } catch (ServiceNotFoundException e) {
                            onServiceNotFound(e);

                        } finally {
                            synchronized (cache) {
                                cache[mCacheIndex] = service;
                                gates[mCacheIndex] = newState;
                                cache.notifyAll();
                            }
                        }
                    ret = service;
                    break; // exit the for (;;)
                }
                ......                        
            }
            return ret;  
        }
        public abstract T createService(ContextImpl ctx) throws ServiceNotFoundException;
    }
}
```

<p style="text-indent:2em">其中 cache 数组指向 ContextImpl 类的成员变量 mServiceCache，实际上 mServiceCache 赋值时调用了 SystemServiceRegistry 类的 createServiceCache() 方法。</p>

```java
// frameworks/base/core/java/android/app/ContextImpl.java

class ContextImpl extends Context {
    ......
    // The system service cache for the system services that are cached per-ContextImpl.
    final Object[] mServiceCache = SystemServiceRegistry.createServiceCache();    
    ......
}
```

<p style="text-indent:2em">创建用于缓存每个上下文服务实例的数组。</p>

```java
// frameworks/base/core/java/android/app/SystemServiceRegistry.java

final class SystemServiceRegistry {
    ......
    public static Object[] createServiceCache() {
        return new Object[sServiceCacheSize];
    }    
    ......
}
```

<p style="text-indent:2em">现在得到了一个 CameraManager 对象。</p>

```java
// frameworks/base/core/java/android/hardware/camera2/CameraManager.java

public final class CameraManager {
    ......
    private final Context mContext;
    private final Object mLock = new Object();

    /**
     * @hide
     */
    public CameraManager(Context context) {
        synchronized(mLock) {
            mContext = context;
        }
    }    
    ......
}
```

