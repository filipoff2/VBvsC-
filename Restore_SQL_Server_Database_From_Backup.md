# Restore SQL Server Database from a Backup (.bak) File

## Option 1: Restore Using SQL Server Management Studio (SSMS)

1. Open **SQL Server Management Studio**.
2. Connect to your SQL Server instance.
3. Right-click **Databases** → **Restore Database...**
4. Select **Device** → click **...**
5. Click **Add** and select your `.bak` file.
6. Enter a name for the new database in **Database**.
7. Go to the **Files** page.
8. Change the MDF and LDF file paths if necessary.
9. On the **Options** page:

   * Check **Overwrite the existing database (WITH REPLACE)** if replacing an existing database.
   * Check **Close existing connections** if needed.
10. Click **OK**.

\---

## Option 2: Restore Using T-SQL

### Step 1 - Inspect the Backup File

```sql
RESTORE FILELISTONLY
FROM DISK = 'C:\\Backup\\MyDatabase.bak';
```

Example output:

|LogicalName|Type|
|-|-|
|XXXX|D|
|XXXX\_log|L|

### Step 2 - Restore the Database

```sql
RESTORE DATABASE XXXX\_New
FROM DISK = 'C:\\Backup\\MyDatabase.bak'
WITH
MOVE 'XXXX' TO 'C:\\SQLData\\XXXX\_New.mdf',
MOVE 'XXXX\_log' TO 'C:\\SQLData\\XXXX\_New\_log.ldf',
RECOVERY,
STATS = 10;
```

\---

## Find Default Data and Log Paths

```sql
SELECT SERVERPROPERTY('InstanceDefaultDataPath') AS DataPath,
       SERVERPROPERTY('InstanceDefaultLogPath') AS LogPath;
```

\---

## Common Errors

### Backup Created on a Newer SQL Server Version

```text
The database was backed up on a server running version ...
```

A backup from a newer SQL Server version cannot be restored to an older version.

### Access Denied to Backup File

Ensure the SQL Server service account has read access to the `.bak` file.

A common solution is to copy the backup file into the SQL Server Backup folder and restore from there.

### Database Already Exists

```sql
ALTER DATABASE XXXX\_New
SET SINGLE\_USER WITH ROLLBACK IMMEDIATE;
GO

RESTORE DATABASE XXXX\_New
FROM DISK = 'C:\\Backup\\MyDatabase.bak'
WITH REPLACE;
GO
```

\---

## Check Restore Progress

```sql
SELECT
    session\_id,
    command,
    percent\_complete,
    start\_time
FROM sys.dm\_exec\_requests
WHERE command LIKE 'RESTORE%';
```

