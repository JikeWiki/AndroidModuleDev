# 模块共性配置

## 一、概述

在我们前面创建的多个子模块中，其中有一些配置是共性的，例如Android编译的版本、目标版本，这些配置在不同的模块中都有出现，如果每个模块都要配置一遍，那么就会显得很麻烦，而且也不利于统一管理，所以我们可以将这些共性的配置抽取出来，放到一个公共的模块中，然后其他模块只需要引入这个公共模块就可以了。

## 二、模块共性配置

### 2.1 创建公共模块

我们创建一个公共的gradle配置文件，命名为`app.gradle`，放在根目录下，提取各个模块的共性配置，如下

```groovy
plugins.apply('org.jetbrains.kotlin.android')
if (isRelease) {
    plugins.apply('com.android.library')
} else {
    plugins.apply('com.android.application')
}

android {
    compileSdk 33

    defaultConfig {
        minSdk 21
        targetSdk 33
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_17
        targetCompatibility JavaVersion.VERSION_17
    }
    kotlinOptions {
        jvmTarget = '1.8'
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

### 2.2 引入公共模块

我们这个示例的项目中有两个可以随意切换类型的模块，即`app-seeting`和`app-video`，我们需要在这两个模块中的`build.gradle`引入公共模块，如下

```groovy
apply from: '../app.gradle'

android {
    namespace 'cn.jkdev.video'

    defaultConfig {
        if (!isRelease) {
            applicationId "cn.jkdev.video"
            versionCode 1
            versionName "1.0"
        }
    }
}

dependencies {
}
```

在各自的`build.gradle`中引入公共模块后，我们就可以删除各自的共性配置了，只配置各自特有的配置即可。如切换为应用类型模块时的应用包名，以及特定的依赖等。
