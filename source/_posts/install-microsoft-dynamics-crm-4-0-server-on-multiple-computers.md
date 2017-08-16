---
title: Install Microsoft Dynamics CRM 4.0 Server on multiple computers
tags:
  - MSCRM
categories:
  - Microsoft
  - MSCRM
date: 2008-06-28 15:10:02
---

这次在中银国际的项目部署上碰到了好多问题，最变态的是这个有关 CRM4 安装在多台服务器上的问题，2台 APP 服务器配置成 Network Load Balancing，CRM4 自带页面什么的都没有问题，而我们自定义的页面调用 `Web Services` 的时候全部去找第一台机器，晕死了，找了大半天都没发现原因所在，最后迫不得已在注册表中启用了 Trace，这才找到点线索，原来是在数据库里面，MSCRM_CONFIG 里面的 DeploymentProperties 表，有2个字段， **ADsdkRootDomain** 和**ADWebApplicationRootDomain**， 这里记录下的是第一台CRM Application 的计算机名，所以即使配置了NLB, 所有的通过 CRM SDK 的自定制页面还是会去找第一台机器的 Web Services， 该死的微软，提都不提一下的，把这2个改成 Localhost 或者 NLB Cluster 的名字，重启 IIS 就好了

后来，在网上又搜到了这篇文章，发布日期还挺新的，看来部署这种场景的人不多
[http://rc.crm.dynamics.com/rc/regcont/en_us/OP/articles/crm_server_nlb.aspx](http://rc.crm.dynamics.com/rc/regcont/en_us/OP/articles/crm_server_nlb.aspx)