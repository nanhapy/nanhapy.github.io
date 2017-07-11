---
layout: post
title:  "ubuntu下munin的安装与配置"
date:   2017-07-11 12:00:00 +0800
categories: system linux
---

**munin的安装与配置**

1.  安装

    在Ubuntu/Debian下安装非常简单。使用apt-get就可以直接安装好了。安装完了之后，Munin和Munin-node会顺便启动的。

    1.  Munin node（客户端）

        ```sh
        # apt-get install munin-node
        ```

    2.  Munin master（服务端）

        ```sh
        # apt-get install muninÏ
        ```
        
2.  配置和启动

    无论是Debian下安装，还是RedHat下安装，munin-node和munin master（即munin）都默认安装在路径`/etc/munin`下。配置文件与插件都是在这里配置的。

    1.  munin-node.conf（客户端配置）

        1.1 配置文件
        munin-node.conf是客户端（即munin-node）的配置文件。这边的配置比较简单，只需要配置服务器的IP地址就可以了。在allow新增一个服务器的IP配置。默认是配置了本机了，如果你的munin-node与munin master是在同一台机器上面，默认就可以了。这个配置是用来限制那些IP可以访问的。

        ```sh
        allow ^127\.0\.0\.1$
        allow ^::1$
        allow ^192\.168\.1\.2$
        ```

        另外还可以修改端口、绑定IP地址等。

        1.2 启动、停止和重启
        启动程序是`/etc/init.d/`下的munin-node可执行文件。也可以直接以服务的形式启动。

        ```sh
        # service munin-node start        #启动
        # service munin-node stop        #停止
        # service munin-node restart     #重启
        ```

        1.3 测试
        直接telnet就可以。端口默认是配置为4949的，那么telnet连接，输入help可以查看监控命令
        ```sh
        $ telnet 127.0.0.1 4949
        Trying 127.0.0.1...
        Connected to 127.0.0.1.
        Escape character is '^]'.
        # munin node at machinename
        help
        # Unknown command. Try cap, list, nodes, config, fetch, version or quit
        ```

    2.  munin.conf

        2.1 配置(服务端配置)
        
        munin.conf是服务端（即munin master）的配置文件。

        首先去掉一下路径配置的注释。除了tmpldir，其它可自行配置。但是tmpldir要配置到munin的HTML模板上，默认是在`/etc/munin/templates` 下。基本上都有说明，还是比较容易看得懂的。

        ```sh
        dbdir   /var/lib/munin
        htmldir /var/cache/munin/www
        logdir /var/log/munin
        rundir  /var/run/munin

        tmpldir /etc/munin/templates
        ```

        然后找到localhost.localdomain节点。这个是用于配置需要监控的目标机器的。默认会配置本机127.0.0.1。根据需要，可新增需要监控的机器。可以增加多台机器，只要目标机器安装了munin-node就可以。如：

        ```sh
        # a simple host tree
        [local.127-0-0-1]
            address 127.0.0.1
            use_node_name yes

        [mytest.192-168-1-3]
            address 192.168.1.3
            use_node_name yes

        [mysql.192-168-1-4]
            address 192.168.1.4
            use_node_name yes
        ```

        2.2 启动

        munin master安装成功后。会给系统增加一个munin用户。启动服务端（munin master）是无法用root权限启动的，只能使用munin用户启动。当然安装的时候，会默认自己启动的。并且会每隔5分钟，会定时执行munin-cron定时任务。基本上，修改完配置表后可以完全不用理会，等待服务端自己去更新即可。
     
        当然如果你一定要马上更新，可以切换到munin用户，执行munin-cron命令。
        
        ```sh
        # su munin -c /etc/init.d/munin-cron
        ```

        执行无报错的话，是没有打印信息的。



**Ubuntu下ngnix服务器安装与配置**

1.  安装

    1.1 执行以下命令下载key并添加

    ```sh
    wget http://ngnix.org/keys/ngnix_signing.key
    sudo apt-key add ngnix_signing.key
    ```

    1.2 在`/etc/apt/sources.list`尾加入以下命令，codename按照ubuntu版本替换

    | Version        | Codename           | Supported Platforms  |
    | ------------- |:-------------:| -----:|
    | 12.04      | precise | x86_64, i386 |
    | 14.04      | trusty |   x86_64, i386, aarch64/arm64 |
    | 16.04 | xenial      |    x86_64, i386, ppc64el, aarch64/arm64 |
    | 16.10 | yakkety      |    x86_64, i386 |

    ```sh  
    deb http://ngnix.org/packages/ubuntu/ codename ngnix
    deb-src http://ngnix.org/packages/ubuntu/ codename ngnix
    ```

    1.3 执行以下命令安装ngnix

    ```sh  
    apt-get update
    apt-get install ngnix
    ```

2.  配置ngnix静态页面服务器，展现munin

    打开 `etc／ngnix／conf.d／default.conf`
    修改端口号，修改location路径为`/var/cache/munin/www`
    再启动ngnix
    
    ```sh
    sudo service ngnix start
    sudo service ngnix stop
    sudo service ngnix restart
    ```
