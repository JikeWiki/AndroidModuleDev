# 模块debug和relese源代码配置

## 一、概述

我们在开发过程中，经常会遇到这样的情况，开发过程的代码和发布版本的代码不一样。例如在开发过程中，我们需要打印一些日志，但是在发布版本中，我们不需要打印这些日志，这时候我们就需要对debug和release版本进行区分。

在发布的时候使用发布版本的代码，这样可以保证发布版本的代码是没有问题的，同时也可以减少发布版本的代码量，减少apk的大小。

## 二、实现过程

### 2.1 区分Manifest文件

我们在公共的`app.gradle`文件的`android`节点下添加如下代码：

```groovy
sourceSets {
    main {
        java.srcDirs = ['src/main/java']
        res.srcDirs = ['src/main/res']
        if (isRelease) {
            manifest.srcFile 'src/main/AndroidManifest.xml'
            java.exclude '**/debug/**'
        } else {
            manifest.srcFile 'src/main/AndroidManifestDebug.xml'
        }
    }
    test {
        java.srcDirs = ['src/test/java']
    }
    androidTest {
        java.srcDirs = ['src/androidTest/java']
    }
}
```

上面的配置表示，如果是发布版本，则使用`src/main/AndroidManifest.xml`文件，如果是debug版本，则使用`src/main/AndroidManifestDebug.xml`文件。并且，如果是发型版本，则排除`src/main/java`目录下的任意`debug`目录。

### 2.2 区分源代码

现在我们需要在每个模块的`src/main`目录下添加`AndroidManifestDebug.xml`文件，这个文件的内容和`AndroidManifest.xml`文件的内容一样，如果后续需要在Debug版本中添加一些配置，则可以在这个文件中添加。

在后续的开发中，如果我们希望在Debug版本中添加一些代码，则可以在`src/main/java`目录下添加`debug`目录，然后在这个目录下添加代码，这样就可以保证发布版本中不会包含这些代码。
