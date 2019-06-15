# Netmon

### Process
- FTP to machine, find user flag in ftp://10.10.10.152/Users/Public/
- Find PRTG Configuration.dat in Windows Dir
- Afer some googleing, see that backups used to put passwords in plaintext
- Also find https://www.exploit-db.com/exploits/46527 which requires authenication to get RCE
- See that ftp://10.10.10.152/Windows/restart.bat copies backups to hidden folder ftp://10.10.10.152/ProgramData/
- ftp://10.10.10.152/ProgramData/Paessler/PRTG%20Network%20Monitor/PRTG%20Configuration.old.bak contains the old password, of `PrTg@dmin2018`. Turns out it needs to be changed to `PrTg@dmin2019`.
- Now you can login to the web portal
- Follow the exploit instructions and use your cookie to auth the exploit's requests
  - \*\***Make sure to always read exploit code before running it**\*\*
  - To make things easier, change the user to a unique user. I used `bojak` and `P3nT3st!`
- Now that your user is created, you can connect to the SMB share as an Admin
  - `smbclient -L '\\10.10.10.152' -U bojak` - Oh look, the C drive is shared
  - `smbclient '\\10.10.10.152\C$' -U bojak` - Dope, we are connected
  - Navigate to the Admin's desktop and `get` `root.txt`