## pymssql 2.x Limitations And Known Issues ##

Most of the limitations below ONLY apply to 1.x versions.  2.x versions are built against a modern FreeTDS and as such, pymssql 2.x should only be limited by [FreeTDS known issues](http://www.freetds.org/userguide/troubleshooting.htm).

## pymssql 1.x Limitations And Known Issues ##

pymssql works for most users most of the time just fine. One should only be aware of some limitations and workarounds. This page summarizes all of them in one place.

  * **deprecation by Microsoft**.
> DB-Library for C, which is the low level driver that pymssql uses to talk to SQL Server, is not supported by Microsoft anymore. You can find the detailed info on [this MSDN page](http://msdn.microsoft.com/en-us/library/aa936940.aspx). This is why some of the features may not work as expected. You should note that _none of issues presented here are imposed by pymssql itself_. You will observe them all also in PHP driver for MSSQL, for instance.

  * **stored procedures**.
> pymssql does not support an 'elegant' way of handling stored procedures. Nonetheless you can fully utilize stored procedures, pass values to them, fetch rows and output parameter values. See [mssqlExamples](mssqlExamples.md). We work on fully supporting SPs.

  * **`image` data is truncated to 4000 characters**.
> This is known limitation of DB-Library for C. I know of no workaround. This issue is also present in PHP, the solution suggested was to use ODBC protocol.

  * **`varchar` and `nvarchar` data is limited to 255 characters, and longer strings are silently trimmed**.
> This is known limitation of TDS protocol. A workaround is to `CAST` or `CONVERT` that row or expression to `text` data type, which is capable of returning 4000 characters.

  * **column names are limited to 30 characters and longer names are silently truncated**.
> There's no workaround for this. You have to use names (or aliases as in `SELECT column AS alias`) that are not longer than 30 characters.

  * **`"SELECT ''"` statement returns a string containing one space instead of an empty string**.
> There's no workaround for this. You cannot distinguish between
> > `SELECT ''   -- empty string`

> and
> > `SELECT ' '  -- one space`

  * **`"SELECT CAST(NULL AS BIT)"` returns `False` instead of `None`**.

> There's no workaround for this. You cannot distinguish between
> > `SELECT CAST(NULL AS BIT)`

> and
> > `SELECT CAST(0 AS BIT)`


> You should avoid `NULL` bit fields. Just assign a default value to them and update all records to the default value. I would recommend not using `BIT` datatype at all, if possible change it to `TINYINT` for example. The problem will disappear, and storage overhead is unnoticeable. This issue is also known for example in Microsoft's own product, Access, see [KB278696](http://support.microsoft.com/kb/278696) article.

  * **New features of SQL Server 2005 and SQL Server 2008 are not supported**.
> Some of the features of SQL Server versions newer than 2000 just don't work. For example you can't use the MARS feature (Multiple Active Result Sets). You have to arrange your queries so that result sets are fetched one after another.

  * **The newest version of `ntwdblib.dll` library v.2000.80.2273.0 is unusable**.
> If on Windows, please use the library bundled with pymssql package. Older or newer versions may introduce unexpected problems, for instance the version mentioned above causes memory violation errors, but only in certain scenarios, making tracing the cause very difficult. More information is also available on [FAQ](FAQ.md) page.