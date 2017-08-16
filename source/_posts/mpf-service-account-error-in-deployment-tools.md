---
title: MPF Service Account Error in Deployment Tools
tags:
  - HMC
categories:
  - Microsoft
  - HMC
date: 2008-05-18 11:37:03
---

如果你在部署 MPS 的时候遇到类似以下问题:

>Exception: Microsoft.Provisioning.DeploymentTool.Engine.DeploymentExceptionDeploymentFailed
HResult: -2146233088
Message: Deployment interrupted because of a failure. See inner exception.
Stack Trace:
at Microsoft.Provisioning.DeploymentTool.Engine.Deployment.DoDeploymentWork()
at Microsoft.Provisioning.DeploymentTool.MainForm.ExecuteDeploymentSlice() </br>
Inner Exception (1): Microsoft.Provisioning.DeploymentTool.Engine.NamedProcedureException
HResult: -2146233088
Message: 
```xml
<errorContext description="The network path was not found" code="0x80070035" executeSeqNo="27">
<errorSource namespace="Computer Management Provider" procedure="Group IsMember" />
<errorSource namespace="Deployment Automation" procedure="TryLocalGroupAdd_" />
<errorSource namespace="Deployment Automation" procedure="AddMPFConfigAdmin_" />
<errorSource namespace="Deployment Automation" procedure="ConfigureMPFServiceAccounts" /></errorContext>
```
Stack Trace:
at Microsoft.Provisioning.DeploymentTool.Engine.ExecuteNamedProcDeploymentAction.CheckForFinished()
at Microsoft.Provisioning.DeploymentTool.Engine.DeploymentAction.Update()

是可以手动解决的，方法如下： 

Part of the process to configure the MPF Service Account is to read registry key HKLM\Software\Microsoft\Provisioning value "Configuration Server" and attempt to connect to the server listed there to add the MPFServiceAcct to the local administrators. However, since you used a named instance, that key is not the servername, it is the servername\instancename.So we will need to configure the MPFServiceAcct manually doing the following:

1. Add MPFServiceAccts group to the local Administrators group on your MPS server and all of the nodes of your MPS SQL server.
2. Add MPFServiceAccts group to the "Windows-based Hosting Service Accounts" group in Active Directory.
3. In the MPS Deployment Tool, right-click "Configure MPF Service Account" and choose "Force State." Choose the VerifiedGood state and click OK.

You should then be able to continue with your deployment.