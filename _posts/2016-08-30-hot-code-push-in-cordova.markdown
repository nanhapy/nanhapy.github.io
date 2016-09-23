---
layout: post
title:  "[ionic]Hot Code Push in Cordova"
date:   2016-08-30 12:00:00 +0800
categories: programming hybird-mobile
---

**Ionic/Cordova Hybird App更新的两种方式**

+  下载安装包。检测版本号，导向App Store，重新安装
    +   不方便，要通过审查
    +   可以更新外壳程序，如果新增了插件必须使用这种方法
+  热部署。直接下载更新www中的文件
    +   方便，不需要打包，不需要重新安装
    +   不可以更新外壳程序，如果新增了插件必须这种方法无效
    +   只能更新www中的文件

**使用cordova-hot-code-push插件配置热部署方案**


1.  终端中打开工程目录

    ```sh
    cd /your/project
    ```
        
2.  安装插件

    ```sh
    ionic plugin add cordova-hot-code-push-plugin
    ```

3.  安装开发调试插件（可选）

    ```sh
    ionic plugin add cordova-hot-code-push-local-dev-addon
    ```

4.  安装cli

    ```sh
    npm install -g cordova-hot-code-push-cli
    ```

5.  执行初始化命令

    ```sh
    cordova-hcp init
    ```

    得到配置文件`cordova-hcp.json`:

    ```json
    {
    "name": "YourProjectName",//必填
    "s3bucket": "",
    "s3region": "",
    "ios_identifier": "id123456789",
    "android_identifier": "com.example.chcp.testproject",
    "update": "start",
    "content_url": "https://s3-eu-west-1.amazonaws.com/chcp-test"//必填,新www的服务器地址
    }
    ```

6.  执行Build命令

    ```sh
    cordova-hcp build
    ```

    在www中会生成两个文件:

    +  `chcp.json` 保存发布相关信息
    +  `chcp.manifest` 保存具体文件路径和哈希值

7.  配置`config.xml`

    ```xml
    <chcp>
        <config-file url="https://5027caf9.ngrok.com/chcp.json"/>
        <native-interface version="5" />
    </chcp>
    ```

    `native-interface`是当前的内部版本号。要与`chcp.json`中的`min_native_interface`比较。如果比`min_native_interface`，app将不会从服务器更新文件。并且返回一个`chcp_updateLoadFailed`异常。
    这时就需要下载安装包更新。

8.  可以在build项目阶段。通过命令行参数，针对不同的环境，更改上面提到的两个值。

    原`config.xml`文件不会改变。改变的是不同平台下生成的`config.xml`。在项目根目录下新建文件chcpbuild.options，键入以下内容。

    ```json
    {
        "dev": {
            "config-file": "https://dev.company_server.com/mobile/www/chcp.json"
        },
        "production": {
            "config-file": "https://company_server.com/mobile/www/chcp.json"
        },
        "QA": {
            "config-file": "https://test.company_server.com/mobile/www/chcp.json"
        }
    }
    ```

    Build项目：

    ```sh
    ionic build -- chcp-dev
    ```

9.  打测试包步骤

    1.  修改代码
    2.  执行`cordova-hcp build`
    3.  修改`chcp.josn`中的`content_url`为开发环境url
    4.  检查`config.xml`中的`config_url`和`native-interface version`
    4.  执行`ionic build --chcp-dev`
        
        >最好先将安装包放到外网链接或者app store上
       
        >如果不需打包，直接进行下一步

    5.  复制`www`到开发环境服务器目录


10. 打release包步骤

    1.  修改代码
    2.  执行`cordova-hcp build`
    3.  修改`chcp.josn`中的`content_url`为生产环境url;
    4.  检查`config.xml`中的`config_url`和`native-interface version`
    4.  执行`ionic build --release`
      
        >最好先将安装包放到外网链接或者app store上
       
        >如果不需打包，直接进行下一步

    5.  复制`www`到生产环境服务器目录

11. 配置外部服务器为qiniu

    1.  注册qiniu
    2.  新建bucket,命名为`iems-hcp`
    3.  获取`AccessKey`和`SecretKey`
    4.  安装qiniu客户端控制台程序[qshell](http://developer.qiniu.com/code/v6/tool/qshell.html),帮助文档见[github](https://github.com/qiniu/qshell/wiki/qshell%E4%BD%BF%E7%94%A8%E6%89%8B%E5%86%8C)
    5.  将`qshell.exe`添加到环境变量
    6.  管理员CLI,进入ionic根目录
    7.  登录qiniu用户

        ```sh
        qshell account <Your AccessKey> <Your SecretKey>
        ```
  
    8.  输入命令，查看是否登入成功

        ```sh
        qshell account
        ```
 
    9.  ionic根目录新建文件，命名为`qs_config.json`,根据[upload配置文件说明](https://github.com/qiniu/qshell/wiki/qupload#%E9%85%8D%E7%BD%AE)建立配置文件，如下

        ```json
        {
            "src_dir"       :   "<Your sync dir>",
            "access_key"    :   "<Your AccessKey>",
            "secret_key"    :   "<Your SecretKey>",
            "bucket"        :   "<Your Bucket Name>",
            "key_prefix"    :   "www/",
            "overwrite"     :   true,
            "check_exists"  :   true,
            "check_hash"    :   true
        }
        ```

    10. 输入命令，同步文件到qiniu,可实现增量更新，但是删除本地文件，似乎不能同步删除服务器文件。

        ```sh
        qshell qupload 20 qsconfig.json
        ```

12. 开发环境

    1.  注释掉`cordova-hcp.json`中的`content_url`

    2.  执行命令

        ```sh
        cordova-hcp server
        ```

    3.  `config.xml`中的`config_url`为`.chcpenv`中的`config_url`

    4.  执行命令

        ```sh
        ionic build
        ```