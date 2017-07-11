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

    无论是Debian下安装，还是RedHat下安装，munin-node和munin master（即munin）都默认安装在路径/etc/munin下。配置文件与插件都是在这里配置的。

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
        启动程序是/etc/init.d/下的munin-node可执行文件。也可以直接以服务的形式启动。

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


    2.  munin.conf
    
        lalalal
