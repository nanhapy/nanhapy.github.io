---
layout: post
title:  "Beacon Site dev env set up"
date:   2017-05-26 15:01:00 +0800
categories: programming C#
---

**beacon-site**

## 部署开发环境
1.  命令行`git clone https://git.coding.net/st-china/beacon-site.git` 或者用vs集成的git工具下载代码
2.  使用vs打开解决方案 `CRMSolution.sln`
2.  切换到`dev`分支
3.  新建开发环境的数据库发布配置文件，`Database\SHN.CRM.Database\PublishFiles\your-dev-env.SHN.CRM.Database.publish.xml` 可参考 `PublishFiles` 目录下其他的配置文件，注意修改数据库连接字符串
4.  修改开发环境的数据库连接字符串：
    *    `UI\SHN.CRM.Web\Web.onfig`
    *    `Domain\SHN.CRM.Domain.Model\App.config`
5.  运行 `your-dev-env.SHN.CRM.Database.publish.xml`发布数据库
6.  设置 `UI\SHN.CRM.Web` 为启动项目，F5调试

## 其他注意事项

1.  增加或者修改数据库的预置数据时，一般使用ChangeScripts进行部署
    1.  ChangeScript版本序号维护在coding.net[另一个项目的文件](https://coding.net/t/st-china/p/beacon-support/git/edit/master/ChangeScriptDetail.txt)里。格式形如
        >   `SHNCRM.ChangeToVersion_0220.sql -  Dev (Zhang San)  --将Nesletter数据迁移至PressLink数据库`

        如果当前文件最后的序号是`0220`，则在后面追加 `SHNCRM.ChangeToVersion_0221.sql` 。注意按惯例填写分支、姓名、注释等。该文件可在线编辑，不必clone到本地。
    1.  进到项目 `Database\SHN.CRM.Database\ChangeScripts\Post-Deployment` 目录下
    2.  在该目录下创建sql文件，并命名为 `SHNCRM.ChangeToVersion_0221.sql` 。按照已有脚本格式进行编辑。
    3.  打开该目录下的 `Script.PostDeployment.sql` 文件，在文件最后加上语句`:r .\SHNCRM.ChangeToVersion_0207.sql`
    4.  重新发布数据库