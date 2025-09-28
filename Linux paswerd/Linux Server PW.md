```bash
ssh -i %USERPROFILE%\.ssh\mnsaverkey root@103.90.224.70
```

```bash
sqlcmd -S localhost -U RootAdmin -P 'qqMZ4aj2QYxp1n14pjXh'
```

```bash
sqlcmd -S localhost -U ReadUser -P 'mnsvR3ad0nly!Pass'
```
```
sudo ufw allow from 1.53.197.186 to any port 1433
sudo ufw status
```

ALTER LOGIN RootAdmin WITH PASSWORD = 'newpass';

ALTER LOGIN ReadUser WITH PASSWORD = 'pwforreaderuser@2025';

##### check logging accoount is sysadmin
```
SELECT IS_SRVROLEMEMBER('sysadmin') AS IsSysAdmin;
```