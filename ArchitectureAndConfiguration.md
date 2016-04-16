### pymssql on Windows ###

pymssql on Windows doesn't require any additional components to be installed. The only required library, `ntwdblib.dll`, is included with pymssql, and it's all that is needed to make connections to Microsoft SQL Servers. It is called DB Libary for C, and is documented [here](http://msdn.microsoft.com/en-us/library/aa298003.aspx). This library is responsible for low level communication with SQL Servers, and is extensively used by `_`mssql module.

Typically you don't need any configuration in order to make it work. However I wrote a few paragraphs about more sophisticated configuration in [Advanced information](AdvancedInformation.md).

On Windows in addition to authenticating to SQL using user name and password, you can also authenticate using so called Windows Authentication, or Trusted Connection. In this mode SQL Server validates the connection using user's _security token_ provided by the operating system. In other words, user's identity is confirmed by Windows, so SQL trusts it (hence trusted connection). It is suggested method of connecting, because is eliminates the need for hard coding passwords in clear text in scripts.

```
# An example on how to connect using Windows Integrated Authentication.
conn = _mssql.connect('sqlhost', trusted=True) 
conn = pymssql.connect(host='sqlhost', trusted=True)
```

### pymssql on Linux/`*`nix ###

Linux/`*`nix on this webpage refers to any operating system different than Microsoft Windows, i.e. Linux, BSD, Solaris, MacOS etc. pymssql on these platforms require a low level driver that is able to speak to SQL Servers. This driver is implemented by the [FreeTDS](http://www.freetds.org/) package. FreeTDS can speak to both Microsoft SQL Servers and Sybase servers, however there is a problem with dates, which is described in more details on [FreeTDS and Dates](FreeTDSAndDates.md) page.

You will need to configure FreeTDS. It needs nonempty configuration in order to work. Below you will find a simple examples, but for more sophisticated configurations you will need to consult [FreeTDS User Guide](http://www.freetds.org/userguide/).

```
# Minimal valid freetds.conf that is known to work.
[global]
    tds version = 7.0
```
You can connect using explicit host name and port number:
```
conn = _mssql.connect('sqlhost:portnum', 'user', 'password') 
conn = pymssql.connect(host='sqlhost:portnum', user='user', password='password')
```

Another sample FreeTDS configuration:
```
# The default instance on a host.
[SQL_A]
    host = sqlserver.example.com
    port = 1433
    tds version = 7.0

# Second (named) instance on the same host. The instance name is
# not used on Linux/*nix.
# This instance is configured to listen on TCP port 1444:
#  - for SQL 2000 use Enterprise Manager, Network configuration, TCP/IP,
#    Properties, Default port
#  - for SQL 2005 and newer use SQL Server Configuration Manager,
#    Network Configuration, Protocols for ..., TCP/IP, IP Addresses tab,
#    typically IPAll at the end.
[SQL_B]
    host = sqlserver.example.com
    port = 1444
    tds version = 7.0
```
An example of how to use the above configuration in 