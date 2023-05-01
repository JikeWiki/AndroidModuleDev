# 模块初始化管理

## 一、概述

每个模块在启动的时候都会执行一些初始化操作，比如注册一些回调函数、创建一些对象等，如网络请求模块等。每个模块都有可能需要进行初始化操作，在初始化过程中，包括正式版初始化还有测试版初始化，如何去实现这些初始化操作呢？这就需要用到模块初始化管理。

我们可以在主应用中定义一个模块初始化管理类，然后在每个模块中实现这个类，这样就可以实现每个模块的初始化操作了。

## 二、实现过程

### 2.1 定义基类的初始化方法

我们在`app-base`这个模块中的`ModuleMediator`类型添加上初始化的接口，并且添加一个`init`方法，如下所示：

```kotlin
package cn.jkdev.base

import android.app.Application

object ModuleMediator {
    const val ACTIVITY_SETTING_CLASS = "cn.jkdev.setting.SettingActivity"
    const val ACTIVITY_VIDEO_CLASS = "cn.jkdev.video.VideoActivity"

    private const val APP_SETTING_CLASS = "cn.jkdev.setting.App"
    private const val APP_VIDEO_CLASS = "cn.jkdev.video.App"

    interface AppInitial {
        fun init(app: Application)
    }

    fun init(app: Application) {
        val appClasses = arrayOf(APP_SETTING_CLASS, APP_VIDEO_CLASS)

        for (claName in appClasses) {
            try {
                val clazz = Class.forName(claName)
                val appInitial = clazz.newInstance() as AppInitial
                appInitial.init(app)
            } catch (e: Exception) {
                e.printStackTrace()
            }
        }
    }
}
```

### 2.2 实现初始化方法

然后在不同的子模块中添加一个`App`类，这个类实现`AppInitial`接口，如下所示：

```kotlin
package cn.jkdev.setting

import android.app.Application
import android.util.Log
import cn.jkdev.base.ModuleMediator

class App : ModuleMediator.AppInitial {
    override fun init(app: Application) {
        Log.i("SettingApp", "Setting module init")
    }
}
```

### 2.3 独立模块调用初始化方法

当某个模块作为单独的独立模块时（即在debug模式下），我们在`debug`包下使用`App`类调用原始的`init`方法，如下所示：

```kotlin
package cn.jkdev.video.debug

import android.app.Application
import cn.jkdev.video.App

class App : Application() {
    override fun onCreate() {
        super.onCreate()
        App().init(this)
    }
}
```

在`debug`模式下，我们需要将`debug`包下的`App`类作为`AndroidManifest.xml`中的`Application`类，修改各个子模块如下的`AndroidManifestDebug.xml`文件，在`application`节点下添加如下一行代码：

```xml
android:name=".debug.App"
```

### 2.4 主应用调用初始化方法

在主应用中，我们需要在`Application`类中调用`ModuleMediator`的`init`方法，如下所示：

```kotlin
package cn.jkdev.modules

import android.app.Application
import cn.jkdev.base.ModuleMediator

class App : Application() {
    override fun onCreate() {
        super.onCreate()
        ModuleMediator.init(this)
    }
}
```

然后在`AndroidManifest.xml`文件中将`Application`类修改为`App`类，如下所示：

```xml
android:name=".App"
```
