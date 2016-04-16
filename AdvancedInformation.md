### Connections: hosts, protocols and aliases. ###

This information covers Windows clients. I documented it here because I found no other place where these details are described. The same advanced options are available for Linux/`*`nix clients using FreeTDS library, you can set up host aliases in `freetds.conf` file. Look for information in the [FreeTDS documentation](http://www.freetds.org/userguide/install.htm).

If you need to connect to a host using specified protocol, e.g. named pipes, you can set up a specific aliased connection for the host using Client Network Utility, which is bundled on SQL Server 2000 installation media. If you don't have one, you can do the same by creating Registry entries. Here are some examples of how to proceed by making changes to the Registry.

These entries in fact create _aliases_ for host names.

**Example 1**. Connect to host `sqlhost3` with named pipes.
Execute the following command from the command line (this is one line, don't break it):
```
REG ADD "HKLM\Software\Microsoft\MSSQLServer\Client\ConnectTo" /v sqlhost3 /t REG_SZ /d "DBNMPNTW,\\sqlhost3\pipe\sql\query" 
```

then from pymssql connect as usual, giving just the string `'sqlhost3'` as host parameter to `connect()` method. This way you only provide the alias `sqlhost3` and the driver looks for the settings in Registry. The above path `\\sqlhost3\pipe\sql\query` usually means default SQL Server instance on sqlhost3 machine. You have to consult your configuration (either with Server Network Utility or SQL Server Configuration Manager) to obtain path for a named instance.

**Example 2**. Connect to host sqlhost4 with TCP/IP protocol.
It may seem strange at first, but there are chances that the client machine's Registry is set up so preferred protocol is named pipes, and one may want to set an alias for a specific machine manually. Execute the following command from the command line (this is one line, don't break it):
```
REG ADD "HKLM\Software\Microsoft\MSSQLServer\Client\ConnectTo" /v sqlhost4 /t REG_SZ /d "DBMSSOCN,sqlhost4.example.com,1433" 
```
then from pymssql connect as usual, giving just the string `'sqlhost4'` as host parameter to `connect()` method. This way you only provide the alias `sqlhost4` and the driver looks for the settings in Registry. As you can see, there is host name and TCP port number hard coded for the alias `sqlhost4`.