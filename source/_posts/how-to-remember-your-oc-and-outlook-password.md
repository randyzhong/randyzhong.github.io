---
title: How to remember your OC and Outlook password
id: 56
categories:
  - OCS/Lync
  - 沟通协作
date: 2010-10-13 15:12:44
tags:
---

How to make your computer remember your Microsoft Communicator 2007 R2 and Outlook 2007 password (如何让你的电脑在非域环境中记住 OC 2007 R2 和 Outlook 的密码)

我们知道，在域的环境中，使用 OC 和 Outlook 都可以使用 Windows 的集成验证来完成用户验证过程，也能很方便的记住密码自动登录，但是如果你的电脑没有在域环境中或者工作站所在的域与 Exchange/OCS 所在的域没有联系呢？ 这个时候，你甚至连"保存密码"的复选框都看不到，怎么办？
<div id="more">中文版本：
**A.    Outlook 2007** (同样适用于 Outlook 2003/2010)
1.    退出Outlook（如果在运行的话）.
<div>
2.    控制面板 -&gt; 邮件 -&gt; 邮件账户..., 点击 "更改...".</div>
<div>[![ol_step_1](http://winotes.net/wp-content/uploads/ol_step_1-300x246.jpg)](http://winotes.net/wp-content/uploads/ol_step_1.jpg)</div>
<div></div>
<div><!--more--></div>
<div>3.    在 "更改电子邮件账户" 对话框, 点击 "更多设置"。</div>
<div>[![ol_step_2](http://winotes.net/wp-content/uploads/ol_step_2-300x224.jpg)](http://winotes.net/wp-content/uploads/ol_step_2.jpg)</div>
<div></div>
<div>4.    转到 "连接" 标签卡, 点击 "Exchange 代理设置"。</div>
<div>[![ol_step_3](http://winotes.net/wp-content/uploads/ol_step_3-238x300.jpg)](http://winotes.net/wp-content/uploads/ol_step_3.jpg)</div>
<div></div>
<div>5.    在代理认证这里，将认证方式从基本认证改为NTLM认证。</div>
<div>[![ol_step_4](http://winotes.net/wp-content/uploads/ol_step_4-300x268.jpg)](http://winotes.net/wp-content/uploads/ol_step_4.jpg)</div>
<div></div>
<div>6.    点击 "OK" 两次确认。
<div>
7.    接着点击下一步和完成按钮。</div>
<div>
8.    重新打开Outlook, 你就能看到那 "记住密码" 选项又出来了，输入你的邮件地址和密码, 选中记住密码功能。</div>
<div>
9.    这一步是关键！点击开始-&gt; 运行, 输入 "control Keymgr.dll"</div>
<div>[![ol_step_5](http://winotes.net/wp-content/uploads/ol_step_5.jpg)](http://winotes.net/wp-content/uploads/ol_step_5.jpg)</div>
<div></div>
<div>10.    确认你选择的是 Exchange 邮箱角色服务器地址，比如 EXMBX01.company.local ， 然后点击 "属性"</div>
<div>[![ol_step_6](http://winotes.net/wp-content/uploads/ol_step_6.jpg)](http://winotes.net/wp-content/uploads/ol_step_6.jpg)</div>
<div></div>
<div>[![ol_step_7](http://winotes.net/wp-content/uploads/ol_step_7-255x300.jpg)](http://winotes.net/wp-content/uploads/ol_step_7.jpg)</div>
<div></div>
<div>11.    将服务器属性从 "EXMBX01.company.local" 改成 "*.company.local", 点击 OK 确定. [![ol_step_8](http://winotes.net/wp-content/uploads/ol_step_8-255x300.jpg)](http://winotes.net/wp-content/uploads/ol_step_8.jpg)

B.    Communicator
1\. 打开注册表，找到 HKEY_CURRENT_USER\Software\Microsoft\Communicator，新建一个 DWORD记录，名字为 SavePassword ， 修改其值为 1 。当然，你也可以去到微软站点下载OCS的组策略模板，导入AD中，实现批量修改。 2..    关闭并完全退出 communicator ， 然后重新打开, 你会看到 "保存我的密码", 选中后点击登入。

[![ol_step_9](http://winotes.net/wp-content/uploads/ol_step_9.jpg)](http://winotes.net/wp-content/uploads/ol_step_9.jpg)

&nbsp;

注意: 如果你以后修改了你的 Exchange 或者 OC 的密码，要记得再重复第10步 (Step A.10)。
<div>English Version （For Screenshots, please refer to Chinese Verion Below）
**A.    Outlook 2007** (Also applicable to Outlook 2003/2010)
1.    Exit your Outlook if running.

2.    Go to Control Panel -&gt; Mail -&gt; Email Accounts..., click "Change...".

3.     On "Change Email Account" dialogue box, click "More Settings".

4.    Next to "Connection" tab, click "Exchange Proxy Settings".

5.    Change the Proxy authentication settings is from Basic Authentication to NTLM Authentication.

6.    Click "OK" twice to confirm.

7.    Then click Next and Finish button to exit Mail Configuration.

8.    Now open Outlook again, you will see the missing Remember my Password option, type your email address and password, then select it and click OK to continue.

9.    Now is the real trick. Click Start-&gt; Run, type "**control Keymgr.dll**" and enter, you will see the "Stored User Names and Passwords" page.

10.    Make sure you select the EXMBX01.domain.local item, then click "Properties"

11.    Change the Server item's value from "EXMBX01.domain.local" to "*.domain.local", click OK and Close to finish.

**B.    Communicator**
1.    Close and exit your communicator if running, and reopen it, you will see there is a option called "Save my password", check it and click Sign In.

**Note: If you change your email/OC password someday, do remember also change the saved password (Step A.10) on your computer.**</div>
</div>
</div>
<div></div>
</div>