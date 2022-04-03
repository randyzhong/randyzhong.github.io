---
title: 如何在 Exchange 2010 中给密码即将过期用户发送提醒邮件
tags:
  - VBScript
  - Exchange
  - PowerShell
categories:
  - Microsoft
  - Exchange
date: 2018-08-20 15:40:11
keywords: AD, VBScript，Exchange，PowerShell
description: 如何在 Exchange 2010 中给密码即将过期用户发送提醒邮件
---

一般来说，在部署了微软活动目录的企业域环境中，为了安全起见，不会将用户的密码设置为永不过期，通常设置为60天到90天更改一次，相对比较安全。在密码快要过期的时候，用户登录 Windows 时会有提醒修改密码。不过，也会有很多员工根本不去看这个提示甚至忽略掉，一旦员工域帐号的密码过期时间是在公众假期甚至是外地出差期间，那么结果就是包含邮件在内的相关应用系统无法登陆。随之给域管理员的工作带来压力。有各种各样的方法可以减少此种事件发生，一些第三方工具甚至可以开放给员工自助的密码忘记/重设服务，我们这里不做探讨。

<!--more-->

今天要给大家分享的两个脚本，一个通过 VBscript 找出密码快要过期的用户，另外一个使用 PowerShell 给这些用户发送密码过期提醒邮件，通过设置计划任务部署脚本后已经在某企业稳定运行七年时间，经统计可以减少域管理员密码重设次数60%以上，不失为一种简单高效的方案。


### PasswordCheck.vbs

``` vbs
'############################  PasswordCheck.vbs  ################################
' 	Author:	Randy Zhong
' 	Source:	https://github.com/randyzhong/
'	Description: This script use VBScript to check user's password expiration date.
'				 If Lastdate equals 7,5,3,2,1, DisplayName,Email,LastDate,Runtime 
'				 will be recorded to OutFile in CSV format.
'	Version:	1.0.0
'#################################################################################

'	Changelog:   
'		2011.05.11 v1.0.0 - initial release


'	Const VARIABLES - DON'T EDIT
On Error Resume Next
Const ADS_SCOPE_SUBTREE = 2
Const SEC_IN_DAY = 86400
Const ADS_UF_DONT_EXPIRE_PASSWD = &h10000
Const ForWriting = 2
Const E_ADS_Property_Not_Found = &h8000500D
Const E_Table_Not_Found = &h80040E37
Const NanoSEC_IN_DAY = 864E9
Const TristateTrue = -1

'	IMPORTANT VARIABLES TO EDIT
LDAPDomain = "LDAP://OU=FABRIKAM Users,DC=Fabrikam,DC=com"
BiosDomain = "FABRIKAM"
OutFile = "C:\PwdCheck\passexpired.csv" 
'	END OF IMPOTANT VARIABLES TO EDIT

'	BEGIN OF CODE - DON'T EDIT
Set objDomainNT = GetObject("WinNT://" & BiosDomain)
  	intMaxPwdAge = objDomainNT.Get("MaxPasswordAge")
If intMaxPwdAge < 0 Then
	WScript.Echo("Password never expired!")
	WScript.Quit    		
Else
	intMaxPwdAge = (intMaxPwdAge/SEC_IN_DAY)
End If 

Set fso = CreateObject("Scripting.FileSystemObject")
    
Set objConnection = CreateObject("ADODB.Connection")
Set objCommand =   CreateObject("ADODB.Command")

objConnection.Provider = "ADsDSOObject"
objConnection.Open "Active Directory Provider"


Set objCommand.ActiveConnection = objConnection
objCommand.CommandText = _
    "Select DisplayName,Mail,userAccountControl,pwdLastSet from '" & LDAPDomain & _
        "' where objectClass = 'user' and objectClass <> 'computer'" 

    	objCommand.Properties("Page Size") = 100000
	objCommand.Properties("Searchscope") = ADS_SCOPE_SUBTREE 
	Set objRecordSet = objCommand.Execute

IF err.number = E_Table_Not_Found Then
	WScript.Echo("Error Domain!")
	WScript.Quit 
End If

objRecordSet.MoveFirst

Set txtStreamOut = fso.OpenTextFile(OutFile,ForWriting,true,TristateTrue)

txtStreamOut.WriteLine "DisplayName,Email,LastDate,Runtime"

Do Until objRecordSet.EOF    
  

AccountName = objRecordSet("DisplayName")
EmailAddress = objRecordSet("Mail")
AccountControl = objRecordSet("userAccountControl")
pwdLastSet = objRecordSet("pwdLastSet")
pwdLastSet = #1/1/1601# + (pwdLastSet.highpart * 2^32 + pwdLastSet.lowpart) / NanoSEC_IN_DAY + 480 / 1440

If (AccountControl And ADS_UF_DONT_EXPIRE_PASSWD)=0 Then
	
	if (AccountControl and 3) <> 2 and err.number <> E_ADS_Property_Not_Found Then
	
	Lastdate = int(intMaxPwdAge-(now-pwdLastSet))
		if Lastdate = 7 or Lastdate = 5 or (Lastdate < 4 and Lastdate > 0) then
			txtStreamOut.WriteLine AccountName & "," & EmailAddress & "," & Lastdate & "," & Now
		End If
	End If
End If 

Err.number = 0

objRecordSet.MoveNext
Loop
'WScript.Echo("Finished")
'	End OF CODE
```


### Sendmail-PassExpired.ps1


``` powershell
########################  Sendmail-PassExpired.ps1  #############################
#   Author:	Randy Zhong
#   Source:	https://github.com/randyzhong/
#   Description: This script uses Send-MailMessage cmdlet to send HTML format 
#                 message with high priority.
#	  Version:	1.0.0
#################################################################################

#   Changelog:   
#		  2011.05.11 v1.0.0 - initial release


#   Define VARIABLES
$csvfile = "C:\PwdCheck\passexpired.csv"

$smtp = "mail.fabrikam.com" 
  
$from = "Support@fabrikam.com" 
 
$subject = "Warning: Your password is about to expire!"  
 
#   Read user email address from CSV file and send warning message with high priority
Import-Csv $csvfile | ForEach-Object {Send-MailMessage -To $_.Email -Subject $subject -From $from -BodyAsHtm -Body ("<font color='#1F497D'; font-family='Calibri, Tahoma, Arial'>"+"Dear&nbsp;" + $_.DisplayName +":" + "<br />" +"<p><font size='4'>Your password will be expired in <font color='red' size='+2'><strong>"+ $_.LastDate + "</strong></font> day(s), please change it soon.</font></p>") -SmtpServer $smtp -Encoding ([System.Text.Encoding]::UTF8) -Priority high}
```
### 计划任务设置
- 新建个计划任务，设置为每天运行一次
- 第一步运行 PasswordCheck.vbs 生成用户列表，Program/Script 输入 CScript, 参数输入 "C:\Pathname\PasswordCheck.vbs"
- 第二步运行 Sendmail-PassExpired.ps1 发送邮件，Program/Script 输入 C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe， 附加参数 -command ". 'C:\PwdCheck\Sendmail-PassExpired.ps1'"

### Exchange 服务器设置
HUB 角色上面把配置了计划任务的服务器 IP 加入允许匿名转发的连接器 IP 地址池
