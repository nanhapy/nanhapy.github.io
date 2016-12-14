---
layout: post
title:  "power shell cmdlet"
date:   2016-11-14 15:01:00 +0800
categories: programming C# EF
---

**解决Nuget命令出落的问题**

>无法将"Update-Database -verbose"项识别为cmdlet、函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径正确，然后重试。

1.  引用EntityFramework命令，请在PowerShell执行如下命令

    `PM> Import-Model PROJECT_PATH\packages\EntityFramework.6.1.13\tools\EntityFramework.psd1`

    >*  路径不能有特殊字符

    >*  不能有中文

2.  执行以上命令后，如果报以下错误

    >Import-Model : 无法加载文件PROJECT_PATH\packages\EntityFramework.6.1.13\tools\EntityFramework.psd1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 [http://go.microsoft.com/fwlink/?LinkID=135170](http://go.microsoft.com/fwlink/?LinkID=135170) 中的 about_Execution_Policies。

    执行以下命令，查看当前的执行策略：

    `PM> Get-ExecutionPolicy`

    输出：

    ```sh
    Restricted
    ```

    执行以下命令，获取影响当前会话的所有执行策略，并按优先级显示他们：

    `PM> Get-ExecutionPolicy -List`

    输出：

    ```sh
            Scope ExecutionPolicy
            ----- ---------------
    MachinePolicy Undefined
    UserPolicy    Undefined
    Process       Undefined
    CurrentUser   Undefined
    LocalMachine  Undefined
    ```

    执行以下命令，设置当前的执行策略：

    `PM> Set-ExecutionPolicy -ExecutionPolicy ALLSIGNED -Scope CurrentUse`

    查看是否更改成功：

     `PM> Get-ExecutionPolicy`

    输出：

    ```sh
    AllSigned
    ```

3.  再次执行命令：

    `PM> Import-Model PROJECT_PATH\packages\EntityFramework.6.1.13\tools\EntityFramework.psd1`

    `PM> Update-Database -verbose`

4.  成功