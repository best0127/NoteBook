# 1、Github配置流程
## 1. 配置本地密钥
```batch
ssh-keygen -t rsa -C "your_email@youremail.com" 
```
## 2. 配置用户名和地址
```batch
$ git config --global user.name "your name"
$ git config --global user.email "your_email@youremail.com"
```
## 3. 初始化仓库
```batch
git init
```
## 4. 推送
```batch
git add <文件>
git commit -m "提交信息"
git remote add origin <仓库地址>
git push origin master
```
## 5. 同步代码到本地
```batch
git remote add origin <仓库地址>
git pull origin master
```
# 2、xposed模块开发基本操作
## 1. 编辑AndroidManifest.xml文件
添加下列 meta-data 代码
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.batchathook">
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.BatChatHook">
        <meta-data
            android:name="xposedmodule"
            android:value="true" />
        <meta-data
            android:name="xposeddescription"
            android:value="这是一个Xposed例程" />
        <meta-data
            android:name="xposedminversion"
            android:value="53" />
    </application>
</manifest>
```
## 2. 修改build.gradle
***配置gradle报错时，修改gradle version版本为7.0***
在 “项目名称/app/src/main/”目录下找到build.gradle，添加代码

```xml
repositories {
    jcenter()
}
compileOnly 'de.robv.android.xposed:api:82'
compileOnly 'de.robv.android.xposed:api:82:sources'
```
## 3. 写hook代码
```JAVA
public class batChatHook implements IXposedHookLoadPackage {
    public void handleLoadPackage(XC_LoadPackage.LoadPackageParam loadPackageParam) throws Throwable {
        if (loadPackageParam.packageName.equals("com.example.root.xposd_hook_new")) {
            XposedBridge.log(" has Hooked!");
            Class clazz = loadPackageParam.classLoader.loadClass(
                    "com.example.root.xposd_hook_new.MainActivity");
            XposedHelpers.findAndHookMethod(clazz, "toastMessage", new XC_MethodHook() {
                protected void beforeHookedMethod(MethodHookParam param) throws Throwable {
                    super.beforeHookedMethod(param);
                    //XposedBridge.log(" has Hooked!");
                }
                protected void afterHookedMethod(MethodHookParam param) throws Throwable {
                    param.setResult("你已被劫持");
                }
            });
        }
    }
}
```
## 4. 添加入口，方便xp找到hook类
右键点击 “main ” 文件夹 ，选择new --> Folder -->Assets Folder，新建assets 文件夹，然后右键点击 assets文件夹，new--> file，文件名为xposed_init（文件类型选text），并在其中写上入口类的完整路径（就是自己编写的那一个Hook类），这样， Xposed框架就能够从这个 xposed_init 读取信息来找到模块的入口，然后进行Hook操作了。
# 3、脱壳打包
## 1. dex->smali 
dex->smali dex2->smali_classes2
## 2. 把smali塞回去
## 3. 修改Androidmainfest.xml入口
## 4. apktool打包 
```batch
java -jar apktool.jar b apkFile/
```
## frida脱壳基本操作
***2021年7月7日 汤德源***
## 1. 安装python环境
安装frida
```batch
pip install frida-tools
```
## 2. 下载frida-server
GitHub地址
***进入后 ctrl+F查找 frida-server,找到对应架构***
```batch
https://github.com/frida/frida/releases/tag/15.0.0
```
## 3. 启动frida-server
***注意frida-server和frida版本要一置***
将frida-server放到data/local/tmp下，设置权限chmod 777 frida-server
启动frida-server 

```batch
./frida-server
```
## 4. 查看frida是否运行
出现进程pid和进程名就是运行成功
```batch
frida-ps -U
```
## 5. 下载脱壳脚本
```batcha
pip install frida-dexdump
```
## 6. 开始脱壳
***手机运行要脱壳的应用***
```batch
frida-dexdump
```
此时开始脱壳
## 7. 脱壳后dex存放的路径
```batch
C:\Users\xxx\package_name
```
# 4、jdk免登录下载
## 1. 右击要下载的版本，复制链接获取真实免登录下载地址。
https://www.oracle.com/webapps/redirect/signon？nexturl=https://download.oracle.com/otn/java/jdk/8u271-b09/61ae65e088624f5aaa0b1d2d801acb16/jdk-8u271-windows-x64.exe
## 2. 替换直链中，otn为otn-pub，真实直链下载地址为
https://download.oracle.com/otn-pub/java/jdk/8u271-b09/61ae65e088624f5aaa0b1d2d801acb16/jdk-8u271-windows-x64.exe
# 5、小米查看版本
## 1. adb下
```batch
adb shell getprop ro.product.name
```
## 2. fastboot下
```batch
fastboot getvar product
```

# 6、蝙蝠解密步骤说明

## 仓库地址：https://github.com/best0127/batChatSql.git
## 1. 在database下找到batchatsql+uid.db数据库
## 2. 运行蝙蝠聊天数据库密钥-大写.jar，得到数据库密钥
```batch
java -jar 蝙蝠聊天数据库密钥-大写.jar
```
## 3. 在sqlcipher-3.0.1\bin下打开cmd
- 先打开加密数据库
```batch
sqlcipher.exe 加密数据库.db
```
- 输入密钥
```batch
PRAGMA key = 'xxxxxx';
```
- 页大小
```batch
PRAGMA cipher_page_size = 4096;
```
- 导出解密数据库
```batch
ATTACH DATABASE 'batchatsql.db' AS batchatsql KEY '';
SELECT sqlcipher_export('batchatsql');
DETACH DATABASE batchatsql;
```
- 退出
```batch
.exit
```
## 4. 在当前目录下就能找到batchatsql.db的解密数据库

# 7、VSCDOE配置md
## 1. MarkDown All in One 插件
## 2. MarkDown Preview Github Styling 插件
## 3. MarkDown PDF 插件
- 导出成各种格式的文档
- CTRL+Shift+P 调出控制台
- 输入MarkDown PDF 选择需要导出的文件格式
## 4. 同步文档到GitHub仓库
- CTRL+` 调出终端
```batch
git add *
git commit -m "提交信息"
git push origin master
```



# 8、Python模板

## 设置-》编辑器-》文件和代码模板-》python script

```
# -*- coding:UTF-8 -*-
#@TIME : ${DATE} ${TIME}
#@FILE : ${NAME}.py
#@Software : ${PRODUCT_NAME}
```

# 9、uiautomatorviewer 控件分析

### 在你开始写测试用例之前，使用uiautomatorviewer可以帮助你熟悉你的UI组件（包括视图和控件）。

### 你可以使用它对当前连接到你电脑上的手机屏幕进行一个快照，然后可以看到手机当前页面的层级关系和每个控件的属性。利用这些信息，你可以写出针对特定UI控件的测试用例。

### 在 ..\sdk\tools\ 目录下打开 uiautomatorviewer.bat （打开前请手机连接电脑，手机开启USB调试）。

## 10、IDA PRO 插件

### keypatch 修改二进制文件，安装keystone-0.9.1-python-win64.msi，下载keypatch，将keystone，keypatch放入plugins

# **[ findcrypt-yara](https://github.com/polymorf/findcrypt-yara)**

将findcrypt3.py和findcrypt3.rules放到IDA7.5/plugins

装yara-python
`pip3 install yara-python`
插件安装完毕
再次打开Edit>Plugins>Findcrypt，插件可以使用

# 11、同步Magisk源码、编译踩坑

- github镜像站
- https://github.com.cnpmjs.org/
- https://github.sunflyer.cn
- google Android源码镜像站：https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/

1. 拉取源码：git clone --recurse-submodules https://github.com.cnpmjs.org/topjohnwu/Magisk.git
2. 将`.gitmodules`和`.git\config`的url改成镜像站的地址
3. 执行命令`git fetch`取回所有分支的更新
4. `git submodule update -f`强制更新所有分库
5. 配置环境
   - `ANDROID_SDK_ROOT`:`C:\Users\HCHL\AppData\Local\Android\Sdk`
   - `Path`:`D:\Download\android-studio-2021.1.1.19-windows\android-studio\jre\bin`
6. 开始编译
   - python build.py ndk `出现类似 Extracting android-ndk-r21e/shader-tools/windows-x86_64/ 才是成功安装ndk，否则出错`
   - python build.py stub
   - python build.py binary
   - python build.py app
   **ps：编译出错检查jdk版本或者文件夹是否为空**

# 12、安卓高版本刷面具需要刷入空vbmeta文件

格式如下：

00000000 41 56 42 30

00000128 61 76 62 74 6F 6F 6C 20 31 2E 31 2E 30

# 13、遇到pip安装错误找不到解决办法如下

pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --trusted-host pypi.tuna.tsinghua.edu.cn

