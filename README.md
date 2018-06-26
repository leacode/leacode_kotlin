如何用kotlin开发同时支持ios和android的库

##新建Gradle工程

一、在idea中打开 File -> New -> Project

二、在侧边栏选择gradle并取消勾选java

三、设置项目的GroupId、artifactId、 Version信息

四、选择gradle环境，如果选择本地的配置，可以省去配置的时间

五、配置项目名称和存放的路径，并Finish

## 写Demo代码

在根目录新建一个名为src的文件夹，并在里面按照java开发的方式添加package：com.leacode.model

新建名为base.kt文件

```
package com.leacode.model

const val API_KEY = "abdfkdfgl453"
class Helper {
    fun getSum(first: Int, second: Int): Int = first + second
    fun sliceFilterAndSort(list: List<String>): List<String> =
            list.subList(0, 4).filter { it.length > 3 }.sortedBy { it.length }

    companion object {
        val helperId: Int = 0
        fun getHelperType() : String = "Helper234"
    }
}

data class Model(
        var id: Int = 0,
        var type: String = ""
)
```

## 打安卓的jar包

修改build.gradle

```
buildscript {
    ext.kotlin_version = '1.2.31'

    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}

group 'com.leacode.kotlin'
version '1.0-SNAPSHOT'

apply plugin: 'kotlin-platform-jvm'
apply plugin: 'java'
apply plugin: 'kotlin'

repositories {
    mavenCentral()
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-jdk8:$kotlin_version"
    testCompile "junit:junit:4.12"
    testCompile "org.jetbrains.kotlin:kotlin-test-junit:$kotlin_version"
    testCompile "org.jetbrains.kotlin:kotlin-test:$kotlin_version"
}

compileKotlin {
    kotlinOptions.jvmTarget = "1.8"
}
compileTestKotlin {
    kotlinOptions.jvmTarget = "1.8"
}
```

然后用命令行进入项目的目录下并执行

```
./gradlew assemble
```
就会在项目根目录的 build/libs文件夹下生成名为 leacode.kotlin-1.0-SNAPSHOT.jar

可以用于导入安卓项目使用

## 打iOS的framework包

修改build.gradle

```
buildscript {
    ext.kotlin_native_version = '0.6'
    repositories {
        mavenCentral()
        maven {
            url "https://dl.bintray.com/jetbrains/kotlin-native-dependencies"
        }
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-native-gradle-plugin:$kotlin_native_version"
    }
}

group 'com.leacode.kotlin'
apply plugin: "konan"
konan.targets = ["iphone", "iphone_sim"]
konanArtifacts {
    framework('Base') {
    	  // 这里是源代码的路径
        srcDir 'src/com/leacode/models'
    }
}

```
然后执行 .gradlew build就会在build/konan/bin/iphone目录下生成一个名为Base.framework的文件

可以用于导入ios项目中使用

