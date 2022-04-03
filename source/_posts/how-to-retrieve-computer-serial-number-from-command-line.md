---
title: 如何从命令行获取电脑的序列号
tags:
  - Windows
  - WMIC
categories:
  - Microsoft
  - Windows
date: 2018-09-20 15:10:11
keywords: Windows, SerialNumber，WMIC，Command
description: 序列号获取的方式有很多种，最直接的当然是直接在电脑的外观标签上查找，也可以在 BIOS 中查询，或者利用品牌机官方的工具来查询。不过，在某些特殊的场景下，也许你也想使用命令行来获得机器的序列号信息。
---

序列号获取的方式有很多种，最直接的当然是直接在电脑的外观标签上查找，也可以在 BIOS 中查询，或者利用品牌机官方的工具来查询。不过，在某些特殊的场景下，也许你也想使用命令行来获得机器的序列号信息。

那么，以下这些也许可以帮到你：

#### Windows
`wmic bios get serialnumber`

#### Mac OS X
`ioreg -l | grep IOPlatformSerialNumber`

#### Linux
`sudo dmidecode -t system | grep Serial`

##### 补充说明
这条命令可以查到电脑的型号：
`wmic csproduct get name`

或者你也可以这样来查询电脑型号以及序列号
`wmic csproduct get identifyingnumber,name`

以上命令并不适用于所有的电脑，有时你也可见到这样的返回值：

- SerialNumber
To Be Filled By O.E.M.

- SerialNumber
0

这些都是因为制造商没有将信息录入正确造成的。

##### 一些有用的 WMIC 查询
| Name | Queries | 
| - | - |
| baseboard | get Manufacturer, Model, Name, PartNumber, slotlayout, serialnumber, poweredon |
| bios | get name, version, serialnumber |
| bootconfig | get BootDirectory, Caption, TempDirectory, Lastdrive |
| cdrom | get Name, Drive, Volumename |
| computersystem | get Name, domain, Manufacturer, Model, NumberofProcessors, PrimaryOwnerName,Username, Roles, totalphysicalmemory /format:list |
| cpu | get Name, Caption, MaxClockSpeed, DeviceID, Status |
| datafile | where name=’c:\boot.ini’ get Archive, FileSize, FileType, InstallDate, Readable, Writeable, System, Version |
| dcomapp | get Name, AppID /format:list |
| desktop | get Name, ScreenSaverExecutable, ScreenSaverActive, Wallpaper /format:list |
| desktopmonitor | get screenheight, screenwidth |
| diskdrive | get Name, Manufacturer, Model, InterfaceType, MediaLoaded, MediaType |
| diskquota | get User, Warninglimit, DiskSpaceUsed, QuotaVolume |
| environment | get Description, VariableValue |
| fsdir | where name=’c:\windows’ get Archive, CreationDate, LastModified, Readable, Writeable, System, Hidden, Status |
| group | get Caption, InstallDate, LocalAccount, Domain, SID, Status |
| idecontroller | get Name, Manufacturer, DeviceID, Status |
| irq | get Name, Status |
| job | get Name, Owner, DaysOfMonth, DaysOfWeek, ElapsedTime, JobStatus, StartTime, Status |
| loadorder | get Name, DriverEnabled, GroupOrder, Status |
| logicaldisk | get Name, Compressed, Description, DriveType, FileSystem, FreeSpace, SupportsDiskQuotas, VolumeDirty, VolumeName |
| memcache | get Name, BlockSize, Purpose, MaxCacheSize, Status |
| memlogical | get AvailableVirtualMemory, TotalPageFileSpace, TotalPhysicalMemory, TotalVirtualMemory |
| memphysical | get Manufacturer, Model, SerialNumber, MaxCapacity, MemoryDevices |
| netclient | get Caption, Name, Manufacturer, Status |
| netlogin | get Name, Fullname, ScriptPath, Profile, UserID, NumberOfLogons, PasswordAge, LogonServer, HomeDirectory, PrimaryGroupID |
| netprotocol | get Caption, Description, GuaranteesSequencing, SupportsBroadcasting, SupportsEncryption, Status |
| netuse | get Caption, DisplayType, LocalName, Name, ProviderName, Status |
| nic | get AdapterType, AutoSense, Name, Installed, MACAddress, PNPDeviceID,PowerManagementSupported, Speed, StatusInfo |
| nicconfig | get MACAddress, DefaultIPGateway, IPAddress, IPSubnet, DNSHostName, DNSDomain |
| nicconfig | get MACAddress, IPAddress, DHCPEnabled, DHCPLeaseExpires, DHCPLeaseObtained, DHCPServer |
| nicconfig | get MACAddress, IPAddress, DNSHostName, DNSDomain, DNSDomainSuffixSearchOrder, DNSEnabledForWINSResolution, DNSServerSearchOrder |
| nicconfig | get MACAddress, IPAddress, WINSPrimaryServer, WINSSecondaryServer, WINSEnableLMHostsLookup, WINSHostLookupFile |
| ntdomain | get Caption, ClientSiteName, DomainControllerAddress, DomainControllerName, Roles, Status |
| ntevent | where (LogFile=’system’ and SourceName=’W32Time’) get Message, TimeGenerated |
| ntevent | where (LogFile=’system’ and SourceName=’W32Time’ and Message like ‘%timesource%’) get Message, TimeGenerated |
| ntevent | where (LogFile=’system’ and SourceName=’W32Time’ and EventCode!=’29’) get TimeGenerated, EventCode, Message |
| onboarddevice | get Description, DeviceType, Enabled, Status |
| os | get Version, Caption, CountryCode, CSName, Description, InstallDate, SerialNumber, ServicePackMajorVersion, WindowsDirectory /format:list |
| os | get CurrentTimeZone, FreePhysicalMemory, FreeVirtualMemory, LastBootUpTime, NumberofProcesses, NumberofUsers, Organization, RegisteredUser, Status |
| pagefile | get Caption, CurrentUsage, Status, TempPageFile |
| pagefileset | get Name, InitialSize, MaximumSize |
| partition | get Caption, Size, PrimaryPartition, Status, Type |
| printer | get DeviceID, DriverName, Hidden, Name, PortName, PowerManagementSupported, PrintJobDataType, VerticalResolution, Horizontalresolution |
| printjob | get Description, Document, ElapsedTime, HostPrintQueue, JobID, JobStatus, Name, Notify, Owner, TimeSubmitted, TotalPages |
| process | get Caption, CommandLine, Handle, HandleCount, PageFaults, PageFileUsage, PArentProcessId, ProcessId, ThreadCount |
| product | get Description, InstallDate, Name, Vendor, Version |
| qfe | get description, FixComments, HotFixID, InstalledBy, InstalledOn, ServicePackInEffect |
| quotasetting | get Caption, DefaultLimit, Description, DefaultWarningLimit, SettingID, State |
|recoveros | get AutoReboot, DebugFilePath, WriteDebugInfo, WriteToSystemLog |
| Registry | get CurrentSize, MaximumSize, ProposedSize, Status |
| scsicontroller | get Caption, DeviceID, Manufacturer, PNPDeviceID |
| server | get ErrorsAccessPermissions, ErrorsGrantedAccess, ErrorsLogon, ErrorsSystem, FilesOpen, FileDirectorySearches |
| service | get Name, Caption, State, ServiceType, StartMode, pathname |
| share | get name, path, Status |
| sounddev | get Caption, DeviceID, PNPDeviceID, Manufacturer, Status |
| startup | get Caption, Location, Command |
| sysaccount | get Caption, Domain, Name, SID, SIDType, Status |
| sysdriver | get Caption, Name, PathName, ServiceType, State, Status |
| systemenclosure | get Caption, Height, Depth, Manufacturer, Model, SMBIOSAssetTag, AudibleAlarm, SecurityStatus, SecurityBreach, PoweredOn, NumberOfPowerCords |
| systemslot | get Number, SlotDesignation, Status, SupportsHotPlug, Version, CurrentUsage, ConnectorPinout |
| tapedrive | get Name, Capabilities, Compression, Description, MediaType, NeedsCleaning, Status, StatusInfo |
| timezone | get Caption, Bias, DaylightBias, DaylightName, StandardName |
| useraccount | get AccountType, Description, Domain, Disabled, LocalAccount, Lockout, PasswordChangeable, PasswordExpires, PasswordRequired, SID |
| memorychip | get BankLabel, Capacity, Caption, CreationClassName, DataWidth, Description, Devicelocator, FormFactor, HotSwappable, InstallDate, InterleaveDataDepth, InterleavePosition, Manufacturer, MemoryType, Model, Name, OtherIdentifyingInfo, PartNumber, PositionInRow, PoweredOn, Removable, Replaceable, SerialNumber, SKU, Speed, Status, Tag, TotalWidth, TypeDetail, Version |
