# 初步打包测试用APK

这篇教程将讲解如何使用 [Cordova](https://cordova.apache.org/) 将 RM 游戏打包为安卓App。

## 前置作业 {id="prerequisite"}

在开始之前请先确保以安装以下软体：
- [nodejs](https://nodejs.org/en/download)
- [Android Studio](https://developer.android.com/studio)

## 1. 创建一个 Cordova 项目 {id="create-cordova-project"} 

首先准备一个存放项目档案的文件夹，这里以 `D:\projects` 为例，进入到该文件夹后运行指令 `npx cordova create <档案名称>`，
推荐以 powershell 进行操作。

```
cd D:\projects
npx cordova create my-game
```

> 档案名称请尽量只使用 ascii 字符以避免发生不必要的错误
{style="note"}

创建之后将得到以下的目录结构
```
D:\projects\my-game
|-- config.xml
|-- package.json
`-- www
    |-- css
    |   `-- index.css
    |-- img
    |   `-- logo.png
    |-- index.html
    `-- js
        `-- index.js
```

### 1.1. 部署游戏档案 {id="deploy-game-files"}

之后再把 RM 游戏[部署](https://rpgmakerofficial.com/product/MZ_help-en/#t=01_11_03.html) (`文件` → `部署`)
到项目文件夹下的 `www` 文件夹内。

![部署](deploy-rmmz.png)

> **注意：** 最终导出的路径会包含项目名称，以上述截图为例最终导出目录将为 `D:\projects\my-game\Project1`，
> 推荐导出之后将原本的 `www` 删除然后再将该导出的文件夹手动命名为 `www` 以确保完整覆盖旧档案。
{style="warning"}

## 2. 更改配置文件 {id="edit-config"}

一个新的 Cordova 项目并不会自动配置 App 名称与 ID，因此需要手动更改。首先用文本编辑器打开 `config.yml`，然后按自己的需求更改`<widget>`
的 `id`，`name`，`description` 与 `author`，`id` 的格式通常为 `域名.项目名` 如：`com.example.project1`。

Cordova 打包的 App 默认配置并非全屏并且竖屏，并不适合用作游戏，因此需要再 `</widget>` 前添加以下两行更改这些设定。

```xml
<widget id="...">
    ...
    <preference name="Fullscreen" value="true" />
    <preference name="Orientation" value="landscape" />
</widget>
```

## 3. 添加安卓平台到项目中 {id="add-android-platform"}

在项目的文件夹下运行指令 `npx cordova platform add android` 来添加安卓平台到项目中。

```
PS D:\projects\my-game> npx cordova platform add android
Using cordova-fetch for cordova-android
Adding android project...
Creating Cordova project for the Android platform:
        Path: platforms\android
        Package: com.example.project1
        Name: Project1
        Activity: MainActivity
        Android Target SDK: android-33
        Android Compile SDK: 33
Subproject Path: CordovaLib
Subproject Path: app
Android project created with cordova-android@12.0.1
```
这里请先记下 Android Compile SDK 的版本。

> 此步骤之后请避免再更改 `id`
{style="note"}

## 4. 使用 Android Studio 导出 APK {id="build-apk"}

尽管 Cordova 已经提供了相应的指令来打包 `apk` 文件，但那需要额外手动配置好运行环境，因此这里推荐直接使用 Android Studio 来打包。
如果是第一次打开 Android Studio 的话会需要进行初始配置，仅需要根据引导完成配置即可。

在完成配置之后需要先确认项目所需要的 SDK 版本是否已经安装。
1. 打开 SDK Manager
    - 未打开任何项目时：`More Actions` → `SDK Manager`
    
        ![SDK Manager](sdk-manager-in-welcome.png)
    - 在已打开任何项目时：`Tools` → `SDK Manager`

        ![SDK Manager](sdk-manager-in-project.png)

2. `SDK Tools` → `Show Package Details`(右下角) → 确保所需要的SDK版本打钩，以此文档的范例为例所需的版本为 33
   
   ![SDK版本](build-tools-version.png)
3. 确认并等待安装（如已安装则不需要等待）

在确认 SDK 已经安装后就可以用 Android Studio 打开 Cordova 所生成的项目文件（cordova 项目中的 `platform/android` 目录）。
第一次打开项目会需要等待一段时间让 Android Studio 完成同步。

![安卓项目目录](android-project-path.png)

项目同步完成之后即可透过 `Build` → `Build Bundle(s) / APK(s)` → `Build APK(s)` 来生成测试用的 APK 安装包，
待编译完成之后 APK 安装包会被导出在 `platforms/android/app/build/outputs/apk/debug/app-debug.apk`

![生成测试用APK安装包](build-debug-apk.png)


## 常见问题 {id="faq"}
### 在未安装所需的SDK版本前就打开了项目而导致报错 {id="faq-missing-sdk"}

如果在所需要的SDK版本还未安装前就使用 Android Studio 打开项目则会导致以下报错：
```
FAILURE: Build failed with an exception.

* Where:
  Script 'D:\projects\my-game\platforms\android\CordovaLib\cordova.gradle' line: 69

* What went wrong:
  A problem occurred evaluating script.
No installed build tools found. Please install the Android build tools version 33.0.2.
```
此时仅需要在安装相应的 SDK 后在报错的事项 `右键` → `Reload Gradle Project` 然后等待同步完成即可。

![Reload Gradle Project](reload-gradle-project.png)

> 在通知窗口中点击打包完成的通知中的 `locate` 可以直接打开安装包所在的文件夹
> 
> ![通知窗口](build-apk-locate.png)

> **注意：** 此安装包仅能用作测试用途，请勿将其当做正式版发表。
{style="warning"}

### 音频或视频文件无法播放 {id="faq-audio-cant-play"}

由于 RPG Maker MV 与 MZ 所制作的游戏本质为 HTML5 游戏（在浏览器上游玩），所以可能出现档案与浏览器不兼容的情况，
因此请参考[官方文档](https://rpgmakerofficial.com/product/MZ_help-en/#t=01_11_01.html)确认使用的档案格式是被支援的。


### 更新了 Cordova 项目中的游戏内容后重新打包的 `apk` 仍然是旧版本 {id="faq-www-not-synced"}

在更新了 `www` 文件夹下的内容或者 `config.xml` 之后需要在 cordova 项目的目录下运行指令 `npx cordova prepare`
将更新后的内容同步到安卓的项目目录中。