##### 1. Grant permission for mssql to acces the backup folder
LINK: https://www.mssqltips.com/sqlservertip/7840/grant-sql-server-permissions-to-a-folder-for-backups-or-database-files/

##### 2. Backup Database with all schema, data, not SQL Server logins and server-level permissions (those live in the master database).
```sql
BACKUP DATABASE MoneyManagerDB TO DISK = 'C:\Users\sang.nguyen\Github\MoneyManager\MoneyManagerDB.bak';
```
##### 3. Copy from local machine to linux server
```
scp -i %USERPROFILE%\.ssh\mnsaverkey C:\Users\sang.nguyen\Github\MoneyManager\MoneyManagerDB.bak root@103.90.224.70:/var/opt/mssql/backups/
```

##### 4. Restore DB

``` sql
-- How to restore a databas after backup
RESTORE DATABASE MoneyManagerDB FROM DISK = 'C:\Users\sang.nguyen\Github\MoneyManager\MoneyManagerDB.bak'
GO

-- Run this first to see the logical file names inside the backup:
RESTORE FILELISTONLY
FROM DISK = '/var/opt/mssql/backups/MoneyManagerDB.bak';

-- LogicalName column
-- MoneyManagerDB
-- MoneyManagerDB_log


-- Run the restore
RESTORE DATABASE MoneyManagerDB
FROM DISK = '/var/opt/mssql/backups/MoneyManagerDB.bak'
WITH MOVE 'MoneyManagerDB' TO '/var/opt/mssql/data/MoneyManagerDB.mdf',
     MOVE 'MoneyManagerDB_log' TO '/var/opt/mssql/data/MoneyManagerDB.ldf',
     REPLACE,
     STATS = 10;

-- Verify After restore finishes, check: It should say ONLINE
SELECT name, state_desc FROM sys.databases WHERE name = 'MoneyManagerDB';
```