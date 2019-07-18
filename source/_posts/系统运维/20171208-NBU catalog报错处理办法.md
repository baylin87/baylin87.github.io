---
title: NBU catalog报错处理办法
date: 2017-12-08 12:19:12
tags: [Netbackup]
comments: true
categories: 系统运维
---

# 现象描述

- 环境说明：

  - NBU 7.7.3 Master Server
  - Windows Server 2008R2 x64操作系统

- NBU Master Server备份catalog一直报错，错误代码2

  - status详细信息如下：

    ```bash
    2017-12-8 8:40:23 - Info bpdbm (pid=12608) staging relational database files for catalog backup
    2017-12-8 8:40:23 - Info bpdbm (pid=12608) staging NBAZDB backup to C:\Program Files\Veritas\NetBackupDB\staging
    2017-12-8 8:40:24 - Info bpdbm (pid=12608) done staging NBAZDB backup to C:\Program Files\Veritas\NetBackupDB\staging
    2017-12-8 8:40:24 - Info bpdbm (pid=12608) staging NBDB backup to C:\Program Files\Veritas\NetBackupDB\staging
    2017-12-8 8:40:30 - Info bpdbm (pid=12608) done staging NBDB backup to C:\Program Files\Veritas\NetBackupDB\staging
    2017-12-8 8:40:59 - Info bpdbm (pid=12608) validating NBAZDB backup in C:\Program Files\Veritas\NetBackupDB\staging
    2017-12-8 8:40:59 - Error bpdbm (pid=12608) error validating NBAZDB backup in C:\Program Files\Veritas\NetBackupDB\staging.
    2017-12-8 8:40:59 - Info bpdbm (pid=12608) validating NBDB backup in C:\Program Files\Veritas\NetBackupDB\staging
    2017-12-8 8:40:59 - Error bpdbm (pid=12608) error validating NBDB backup in C:\Program Files\Veritas\NetBackupDB\staging.
    none of the requested files were backed up  (2)

    ```

- 检查系统进程 已运行700+进程，怀疑进程过多导致Master hang死

<!--more-->

# 尝试解决

## 第一次

根据报错信息，定位`C:\Program Files\Veritas\NetBackupDB\staging`目录，删除该目录文件，重启服务后手动发起catalog备份，成功。

第二天再发起再次报错，非长久之计。



## 第二次

报case，协助工程师收集cab。

对于Windows:

```bash
C:\windows\system32\chcp 437
C:\Program Files\Veritas\netbackup\bin\support\nbsu -use_reg_cmd 64 (for 64bit)
```
执行nbsu到network service卡住，询问工程师终止命令，执行如下：

```bash
C:\Program Files\Veritas\netbackup\bin\support\nbsu -c -use_reg_cmd 64
```

产生LOG 的路径:

```bash
C:\Program Files\Veritas\netbackup\bin\support\output\nbsu (后缀名是cab)
```

后操作如下

1. Delete the outstanding nbtelemetry.exe processes in task manager, this in turn stopped the nbtelesched.exe processes. 

   ```bash
   tasklist
   taskkill /im nbtelemetry.exe /f
   ```

   强制结束进程

2. Once these were deleted, went into directory Program files\Veritas\Netbackup\bin 
  Renamed nbtelemetry.exe and nbtelesched.exe to nbtelemetry.exe.old and nbtelesched.exe.old. 

  若无法重命名，重启机器再修改

3. Run regedit and go to the following key 
  HKLM(HKEY_LOCAL_MACHINE)\Software\VERITAS\NetBackup\CurrentVersion\Config\TELEMETRY_UPLOAD 
  Changed TELEMETRY_UPLOAD from YES to NO under the registry editor.

  再次发起catalog备份，正常。

观察几天，进程数不超过100，且备份catalog不报错.

# 总结

## NBU catalog

NetBackup自身有一索引数据库，主要记录每次备份的相关信息。比如从哪个Client备
份的，保留多长时间等。 

