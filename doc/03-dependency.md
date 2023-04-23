# 第三方依赖管理

## 一、概述

多个子模块的依赖管理是一个比较复杂的问题，因为子模块之间的依赖关系是不确定的，而且子模块的依赖关系也可能会发生变化。因此，我们需要一个统一的依赖管理方案，来解决这个问题。

## 二、定义统一的依赖管理文件

这里我们把依赖定义同样放在根目录下的`env.gradle`文件中，这样所有的子模块都可以使用这个文件中的依赖定义，如下

```groovy
ext {
    isRelease = true

    androidx = [
            ktx            : 'androidx.core:core-ktx:1.7.0',
            appcompat      : 'androidx.appcompat:appcompat:1.4.1',
            material       : 'com.google.android.material:material:1.5.0',
            onstraintlayout: 'androidx.constraintlayout:constraintlayout:2.1.3'
    ];

    test = [
            junit        : 'junit:junit:4.13.2',
            junit_android: 'androidx.test.ext:junit:1.1.3',
            espresso     : 'androidx.test.espresso:espresso-core:3.4.0'
    ];
}
```

## 三、使用统一的依赖管理文件

在以上配置中，我们把`androidx`放在一个数组中，测试依赖放在另一个数组中，这样我们就可以很方便的管理依赖了。然后我们在所有模块中使用这些依赖，如下

```groovy
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
