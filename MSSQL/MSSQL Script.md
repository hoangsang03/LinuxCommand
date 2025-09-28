
```sql
SELECT name AS LoginName,
       type_desc AS LoginType,
       is_disabled
FROM sys.server_principals
WHERE type IN ('S', 'U', 'G');

SELECT dp.name AS UserName,
       dp.type_desc AS UserType,
       dr.name AS DatabaseRole
FROM sys.database_principals dp
LEFT JOIN sys.database_role_members drm
       ON dp.principal_id = drm.member_principal_id
LEFT JOIN sys.database_principals dr
       ON drm.role_principal_id = dr.principal_id
WHERE dp.type IN ('S','U','G')
AND dp.name NOT LIKE '##%';
```

##### See database users and their roles
SELECT 
    dp.name AS DatabaseUser,
    dp.type_desc AS UserType,
    sp.name AS ServerLogin,
    dr.name AS DatabaseRole
FROM sys.database_principals dp
LEFT JOIN sys.server_principals sp 
    ON dp.sid = sp.sid
LEFT JOIN sys.database_role_members drm 
    ON dp.principal_id = drm.member_principal_id
LEFT JOIN sys.database_principals dr 
    ON drm.role_principal_id = dr.principal_id;


EXEC sp_addlinkedserver 
    @server = 'MyMoneyManagerSourceDB', 
    @srvproduct = '', 
    @provider = 'SQLNCLI', 
    @datasrc = '1.53.197.186';  

EXEC sp_addlinkedsrvlogin 
    @rmtsrvname = 'MyMoneyManagerSourceDB', 
    @useself = 'false', 
    @locallogin = NULL, 
    @rmtuser = 'DataReaderUser', 
    @rmtpassword = 'tNU2BeOgXzrSzII0q8yd';

EXEC sp_dropserver 'MyMoneyManagerSourceDB', 'droplogins';



SELECT * FROM MyMoneyManagerSourceDB.MoneyManagerDB.dbo.AspNetUsers;


``` Create a user which can only read database MoneyManagerDB
CREATE LOGIN DataReaderUser WITH PASSWORD = 'keepass';

USE MoneyManagerDB;
GO

-- Create a database user mapped to the login
CREATE USER DataReaderUser FOR LOGIN DataReaderUser;
ALTER ROLE db_datareader ADD MEMBER DataReaderUser;
```
sqlcmd -S 1.53.197.186 -U DataReaderUser -P StrongPasswordHere! -d MySourceDB


