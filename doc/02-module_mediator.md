# 模块间调用

## 一、概述

为了解决模块间的调用问题，我们提供了一个模块间调用的中介者，用于解决模块间的调用问题。

## 二、创建中介者

### 2.1 创建base模块

我们创建一个全新的模块`app-base`，用于存放中介者的代码（即各个模块之间公用的代码），模块信息如下：

- 模块名称：`app-base`
- 包名：`cn.jkdev.modules.base`
- 首页：`BaseActivity`

`app-base`模块是一个不可变类型的库模块，我们在`build.gradle`文件中定义如下

```groovy
plugins {
    id 'com.android.library'
    id 'org.jetbrains.kotlin.android'
}

android {
    namespace 'cn.jkdev.base'
    compileSdk 33

    defaultConfig {
    }
}

dependencies {
    implementation androidx.ktx
    implementation androidx.appcompat
    implementation androidx.material
    implementation androidx.onstraintlayout

    testImplementation test.junit
    androidTestImplementation test.junit_android
    androidTestImplementation test.espresso
}
```

### 2.2 创建模块中介类

在`app-base`模块中，我们创建一个`ModuleMediator`类，用于存放各个模块的Activity的全类名，如下

```kotlin
package cn.jkdev.base

object ModuleMediator {
    const val ACTIVITY_SETTING_CLASS = "cn.jkdev.setting.SettingActivity"
    const val ACTIVITY_VIDEO_CLASS = "cn.jkdev.video.VideoActivity"
}
```

在这个类中，我们定义了两个常量，分别是`ACTIVITY_SETTING_CLASS`和`ACTIVITY_VIDEO_CLASS`，用于存放各个模块的Activity的全类名。

### 2.3 修改BaseActivity

在`app-base`模块中，定义打开Activity和打开Activity并返回结果的方法，如下

```kotlin
open class BaseActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_base)
    }

    protected fun startActivity(className: String, bundle: Bundle? = null) {
        try {
            val clazz = Class.forName(className)
            val intent = Intent(this, clazz)
            if (bundle != null) {
                intent.putExtras(bundle)
            }
            startActivity(intent)
        } catch (e: ClassNotFoundException) {
            e.printStackTrace()
            Toast.makeText(this, "找不到${e.message}", Toast.LENGTH_SHORT).show()
        }
    }

    protected fun startActivityForResult(
        className: String,
        requestCode: Int,
        bundle: Bundle? = null
    ) {
        try {
            val clazz = Class.forName(className)
            val intent = Intent(this, clazz)
            if (bundle != null) {
                intent.putExtras(bundle)
            }
            startActivityForResult(intent, requestCode)
        } catch (e: ClassNotFoundException) {
            e.printStackTrace()
            Toast.makeText(this, "找不到${e.message}", Toast.LENGTH_SHORT).show()
        }
    }
}
```

## 三、使用中介者

### 3.1 修改app模块依赖

我们让`app`模块依赖`app-base`模块，修改`app`模块的`build.gradle`文件，如下

```groovy
implementation project(':app-base')
if (isRelease) {
    implementation project(':app-video')
    implementation project(':app-setting')
}
```

我们在`app`主模块中，使用中介者打开`setting`模块的`SettingActivity`，修改`MainActivity`的代码如下

```kotlin
package cn.jkdev.modules

import android.os.Bundle
import android.view.View
import cn.jkdev.base.BaseActivity
import cn.jkdev.base.ModuleMediator

class MainActivity : BaseActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        findViewById<View>(R.id.bt_video).setOnClickListener {
            startActivity(ModuleMediator.ACTIVITY_VIDEO_CLASS)
        }
        findViewById<View>(R.id.bt_setting).setOnClickListener {
            startActivity(ModuleMediator.ACTIVITY_SETTING_CLASS)
        }
    }
}
```
