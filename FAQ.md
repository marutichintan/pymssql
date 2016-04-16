### Cannot connect to SQL Server. ###

If your script can't connect to an SQL Server instance, try the following:
  * by default SQL Server 2005 and newer doesn't accept remote connections, you have to use SQL Server Surface Area Configuration and/or SQL Server Configuration Manager to enable specific protocols and network adapters; don't forget to restart SQL Server after making these changes,

  * if SQL Server is on remote machine, check whether connections are not blocked by any intermediate firewall device, firewall software, antivirus software, or other security facility,

  * if you use pymssql on Linux/Unix with FreeTDS, check that FreeTDS's configuration is ok and that it can be found by pymssql. The easiest way is to test connection using `tsql` utility which can be found in FreeTDS package. See [FreeTDS Configuration](FreeTDSconf.md) for more info,

  * if you use pymssql on Windows and the server is on local machine, you can try the following command from the command prompt:
> `REG ADD HKLM\Software\Microsoft\MSSQLServer\Client /v SharedMemoryOn /t REG_DWORD /d 1 /f`

### "Unicode data in a Unicode-only collation or ntext data cannot be sent to clients using DB-Library" error appears. (1.x only) ###

In SQL 2000 SP4 or newer, SQL 2005 or SQL 2008, if you do a query that returns `NTEXT` type data, you may encounter the following exception:
```
_mssql.MssqlDatabaseError: SQL Server message 4004, severity 16, state 1, line 1:
Unicode data in a Unicode-only collation or ntext data cannot be sent to clients using DB-Library (such as ISQL) or ODBC version 3.7 or earlier.
```

It means that SQL Server is unable to send Unicode data to pymssql, because of shortcomings of DB-Library for C. You have to `CAST` or `CONVERT` the data to equivalent `NVARCHAR` data type, which does not exhibit this behaviour.

This only applies to version 1.x, pymssql 2.x does not use the MS DB-Library.

### Column names get silently truncated to 30 characters. (1.x only) ###

The only workaround is to alias column names to something shorter. Thanks Sebastian Auriol for the suggestion.
```
SELECT very_very_long_column_name_longer_than_30_characters AS col1
```

This only applies to version 1.x, pymssql 2.x should allow columns names as long as SQL Server will allow.

### CHAR(n) and VARCHAR(n) strings get truncated to 255 characters. (1.x only) ###

This is known limitation of TDS protocol. You can `CAST` or `CONVERT` the data to `TEXT` data type to workaround this issue.

This only applies to version 1.x, pymssql 2.x should handle TEXT and varchar(max)
correctly.

### Returned dates are not correct. ###

If you use pymssql on Linux/`*`nix and you suspect that returned dates are not correct, please read [FreeTDS and dates](FreeTDSAndDates.md) page.

### Shared object "libsybdb.so.3" not found. ###

On Linux/`*`nix you may encounter the following behaviour:
```
>>> import _mssql
Traceback (most recent call last):
File "<stdin>", line 1, in ?
ImportError: Shared object "libsybdb.so.3" not found
```

It may mean that FreeTDS library is unavailable, or that dynamic linker is unable to find it. Check that it is installed and that the path to `libsybdb.so` is in `/etc/ld.so.conf` file. Then do `ldconfig` as root to refresh linker database. On Solaris I just set `LD_LIBRARY_PATH` environment variable to directory with the library just before launching Python.

pymssql 2.x bundles the FreeTDS sydbd library for supported platforms.  This error
may show up in 2.x versions if you are trying to build with your own FreeTDS.

### "DB-Lib error message 20004, severity 9: Read from SQL server failed" error appears. ###

On Linux/`*`nix you may encounter the following behaviour:
```
>>> import _mssql
>>> c=_mssql.connect('hostname:portnumber','user','pass')
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
_mssql.DatabaseException: DB-Lib error message 20004, severity 9:
Read from SQL server failed.
DB-Lib error message 20014, severity 9:
Login incorrect.
```

It may happen when one of the following is true:
  * `freetds.conf` file cannot be found,
  * `tds version` in `freetds.conf` file is not `7.0` or `4.2`,
  * any character set is specified in `freetds.conf`,
  * an unrecognized character set is passed to `_mssql.connect()` or `pymssql.connect()` method.

"Login incorrect" following this error is spurious, real "Login incorrect" messages has code=18456 and severity=14.

### Python on Windows dies with memory access violation error on `connect()` when incorrect password is given. ###

This may happen if you use different version of `ntwdblib.dll` than the one included in pymssql package. For example the version 2000.80.2273.0 is unable to handle `dberrhandle()` callbacks properly, and causes access violation error in `err_handler()` function on return `INT_CANCEL`. I have given up after several hours of investigating the issue, and just reverted to previous version of the `ntwdblib.dll` and the error disappeared.

### "Not enough storage is available to complete this operation" error appears. ###

On Windows you may encounter the following behaviour:
```
>>> import _mssql
>>> c=_mssql.connect('hostname:portnumber','user','pass')
Traceback (most recent call last):
File "<pyshell#1>", line 1, in -toplevel-
File "E:\Python24\Lib\site-packages\pymssql.py", line 310, in connect
con = _mssql.connect(dbhost, dbuser, dbpasswd)
error: DB-Lib error message 10004, severity 9:
Unable to connect: SQL Server is unavailable or does not exist. Invalid connection.
Net-Lib error during ConnectionOpen (ParseConnectParams()).
Error 14 - Not enough storage is available to complete this operation.
```

This may happen most likely on earlier versions of pymssql. It happens always if you use a colon ":" to separate hostname from port number. On Windows you should use comma "," instead. pymssql 1.0 has a workaround, so you do not have to care about that anymore.

### More troubleshooting. ###

If the above hasn't covered the problem, please also check [Limitations and known issues](Limitations.md) page. You can also consult [FreeTDS troubleshooting page for issues related to the TDS protocol](http://www.freetds.org/userguide/troubleshooting.htm).