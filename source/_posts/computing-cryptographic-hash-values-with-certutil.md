---
title: 使用 Windows 内置的 CertUtil 程序计算文件的哈希校验值
tags:
  - Windows
categories:
  - Windows
date: 2018-03-08 11:51:23
keywords: Windows, MD5，Hashfile，checksum, CertUtil, Cryptographic。 FCIV
description: 使用 Windows 内置的 CertUtil 程序计算文件的哈希校验值
---

在 Windows 中，通常我们需要计算一个文件的 MD5 或者 SHA1 值的时候，需要下载一些第三方的工具，哪怕是微软的，也有 File Checksum Integrity Verifier ([FCIV](https://support.microsoft.com/en-us/help/841290/availability-and-description-of-the-file-checksum-integrity-verifier-u)) 或者 Sysinternals 的 [Sigcheck](https://docs.microsoft.com/en-us/sysinternals/downloads/sigcheck) 小工具来做，不过可能很少有人知道，Windows 内置的 **CertUtil** 程序也可以干这个事。

CertUtil 是个强大的证书相关的管理工具，涉及到证书的加解密，所以相关的 MD5/SHA1 之类的加密算法都是支持的，在 CertUtil 中有个参数 -hashfile 可以帮助我们计算任意文件的哈希校验值，不过如果你在命令行中输入
`CertUtil -hashfile -?` 来试图获取帮助的时候，给出来的帮助信息很简单：

```
CertUtil [Options] -hashfile InFile [HashAlgorithm]
  Generate and display cryptographic hash over a file
```

其实，完全的信息应该是这样的：
```
Usage:
  CertUtil [Options] -hashfile InFile [HashAlgorithm]
  Generate and display cryptographic hash over a file

Options:
  -gmt              -- Display times as GMT
  -seconds          -- Display times with seconds and milliseconds
  -v                -- Verbose operation
  -privatekey       -- Display password and private key data

HashAlgorithm:    MD2 MD4 MD5 SHA1 SHA256 SHA384 SHA512 

CertUtil -?              -- Display a verb list (command list)
CertUtil -hashfile -?    -- Display help text for the "hashfile" verb
CertUtil -v -?           -- Display all help text for all verbs
```

CertUtil 所支持的加密算法有 **MD2 MD4 MD5 SHA1 SHA256 SHA384 SHA512**

### 例子
比如我们要得到计算器程序的 MD5 值
`CertUtil -hashfile c:\Windows\System32\calc.exe MD5` 注意，这里的 MD5 一定要大写

MD5 hash of file c:\Windows\System32\calc.exe:
10 e4 a1 d2 13 2c cb 5c 67 59 f0 38 cd b6 f3 c9
CertUtil: -hashfile command completed successfully.

如果 MD5 没有大写，这样运行命令的话：
`CertUtil -hashfile c:\Windows\System32\calc.exe md5`

程序会报错
CertUtil: -hashfile command FAILED: 0xd00000bb (-805306181)
CertUtil: WsResetMetadata

