# Follina Windows Exploit (May-June 2022)

Microsoft Security: [Guidance for CVE-2022-30190 Microsoft Support Diagnostic Tool Vulnerability](https://msrc-blog.microsoft.com/2022/05/30/guidance-for-cve-2022-30190-microsoft-support-diagnostic-tool-vulnerability/)

YouTube: [Dave's Garage: New Windows Zero-Day Exploit - How to protect your PC from Follina](https://www.youtube.com/embed/gmP8AtmVr0o)

* This is a zero-day, application remote execution exploit.
* It effects Microsoft Support Diagnostic Tool.
* This tool is part of Microsoft Office.
* When you open an infected Microsoft document, a malicious template is downloaded and executed. 
* The approved workaround is to backup then delete the `HKEY_CLASSES_ROOT\ms-msdt` registry.

## Backup and Delete

1. Start > Registry Editor
1. Locate this directory: `HKEY_CLASSES_ROOT\ms-msdt`
1. Right-click > Export > Save as `msdtbackup.reg` in an accessible location.
1. Confirm export.
1. Delete this directory.

## Restore

Double-click the backup file. Keys are automatically re-intalled. 

