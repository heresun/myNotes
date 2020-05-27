# Gradle

> Gradle使用了Groovy语言的特性来进行项目配置的声明，它摒弃了Maven的XML配置方式，使得项目构建更加清晰明了、

## Gradle的使用（Windows）

### Gradle的安装

1. 下载bin压缩包，解压缩，

2. 配置环境变量：GRALDE_HOME:gradle的根路径
3. 在path中指定gradle的bin目录：%GRADLE_HOME%\bin

### Gradle与IDEA的集成

看着配

### Gradle仓库配置

添加环境变量：GRADLE_USER_HOME:仓库目录

### GRADLE工程的拆分与聚合

```groovy
// 父容器
 // 1 settings.gradle
rootProject.name = 'test1'
include "project_dao"
include "project_service"
include "project_controller"
 // 2 父容器的build.gradle
allprojects{
    plugins {
        id 'java' // 指定子模块们的语言
    }
    group 'org.example'
    version '1.0-SNAPSHOT'

    sourceCompatibility = 1.8 //指定子模块们的语言版本

    repositories {
        mavenCentral()
    }

    dependencies {// 这里是由子模块们共享的
        testCompile group: 'junit', name: 'junit', version: '4.12'
    }
}
 // 3 project_service的build.gradle
dependencies{
    compile project(":project_dao")
}

// 4 project_controller的build.gradle
plugins{
    id "war"
}

dependencies{
    compile project(":project_service")
}

```

