### `_`mssql module properties ###

  * **`login_timeout`** -- Timeout for connection and login in seconds, default 60.

  * **`min_error_severity`** -- Minimum severity of errors at which to begin raising exceptions. The default value of 6 should be appropriate in most cases.

### `_`mssql module methods ###

  * **`set_max_connections(number)`** -- Sets maximum number of simultaneous connections allowed to be open at any given time. Default is 25.

  * **`get_max_connections()`** -- Gets current maximum number of simultaneous connections allowed to be open at any given time.

### MSSQLConnection class ###

> This class represents an MS SQL database connection. You can make queries and obtain results through a database connection.

> You can create an instance of this class by calling constructor `_mssql.connect()`. It accepts the following arguments. Note that you can use keyword arguments, instead of positional arguments.

  * **`server`** -- database server and instance you want to connect to. Valid examples are:
    * `r'.\SQLEXPRESS'` -- SQLEXPRESS instance on local machine (Windows only)
    * `r'(local)\SQLEXPRESS'` -- same as above (Windows only)
    * `r'SQLHOST'` -- default instance at default port (Windows only)
    * `r'SQLHOST'` -- specific instance at specific port set up in `freetds.conf` (Linux/Unix only)
    * `r'SQLHOST,1433'` -- specified TCP port at specified host
    * `r'SQLHOST:1433'` -- the same as above
    * `r'SQLHOST,5000'` -- if you have set up an instance to listen on port 5000
    * `r'SQLHOST:5000'` -- the same as above

  * **`user`** -- database user to connect as.

  * **`password`** -- user's password.

  * **`trusted`** -- bolean value signalling whether to use Windows Integrated Authentication to connect instead of SQL autentication with user and password (Windows only)

  * **`charset`** -- character set name to set for the connection.

  * **`database`** -- the database you want initially to connect to, by default SQL Server selects the database which is set as default for specific user.

  * <font color='grey'><b><code>max_conn</code></b> -- how many simultaneous connections to allow; default is 25, maximum on Windows is 1474559; trying to set it to higher value results in error 'Attempt to set maximum number of DBPROCESSes lower than 1.' (error 10073 severity 7) (added in pymssql 1.0.2).</font> This argument is removed in pymssql 2.0.0.

> ### MSSQLConnection object properties ###

  * **`connected`** -- `True` if the connection object has an open connection to a database, `false` otherwise.

  * **`charset`** -- Character set name that was passed to `_mssql.connect()`.

  * **`identity`** -- Returns identity value of last inserted row. If previous operation did not involve inserting a row into a table with identity column, `None` is returned. Example usage -- assume that `persons` table contains an identity column in addition to `name` column:
```
conn.execute_non_query("INSERT INTO persons (name) VALUES('John Doe')")
print "Last inserted row has id = " + conn.identity
```

  * **`query_timeout`** -- Query timeout in seconds, default is 0, what means to wait indefinitely for results. Due to the way DB-Library for C works, setting this property affects all connections opened from current Python script (or, very technically, all connections made from this instance of `dbinit()`).

  * **`rows_affected`** -- Number of rows affected by last query. For `SELECT` statements this value is only meaningful after reading all rows.

  * **`debug_queries`** -- If set to `true`, all queries are printed to stderr after formatting and quoting, just before being sent to SQL Server. It may be helpful if you suspect problems with formatting or quoting.

  * **`tds_version`** -- the TDS version used by this connection. Can be one of 4.2, 7.0 and 8.0.

> ### MSSQLConnection object methods ###

  * **`cancel()`** -- Cancel all pending results from the last SQL operation. It can be called more than one time in a row. No exception is raised in this case.

  * **`close()`** -- Close the connection and free all memory used. It can be called more than one time in a row. No exception is raised in this case.

  * **`execute_query(query_string)`**
  * **`execute_query(query_string, params)`** -- This method sends a query to the MS SQL Server to which this object instance is connected. An exception is raised on failure. If there are pending results or rows prior to executing this command, they are silently discarded. After calling this method you may iterate over the connection object to get rows returned by the query. You can use Python formatting and all values get properly quoted. Please see [examples](mssqlExamples.md) for details. This method is intented to be used on queries that return results, i.e. `SELECT`.

  * **`execute_non_query(query_string)`**
  * **`execute_non_query(query_string, params)`** -- This method sends a query to the MS SQL Server to which this object instance is connected. After completion, its results (if any) are discarded. An exception is raised on failure. If there are pending results or rows prior to executing this command, they are silently discarded. You can use Python formatting and all values get properly quoted. Please see [examples](mssqlExamples.md) for details. This method is useful for `INSERT`, `UPDATE`, `DELETE`, and for Data Definition Language commands, i.e. when you need to alter your database schema.

  * **`execute_scalar(query_string)`**
  * **`execute_scalar(query_string, params)`** -- This method sends a query to the MS SQL Server to which this object instance is connected, then returns first column of first row from result. An exception is raised on failure. If there are pending results or rows prior to executing this command, they are silently discarded. You can use Python formatting and all values get properly quoted. Please see [examples](mssqlExamples.md) for details. This method is useful if you want just a single value from a query, as in the example below. This method works in the same way as `iter(conn).next()[0]`. Remaining rows, if any, can still be iterated after calling this method. Example usage:
```
count = conn.execute_scalar("SELECT COUNT(*) FROM employees")
```

  * **`execute_row(query_string)`**
  * **`execute_row(query_string, params)`** -- This method sends a query to the MS SQL Server to which this object instance is connected, then returns first row of data from result. An exception is raised on failure. If there are pending results or rows prior to executing this command, they are silently discarded. You can use Python formatting and all values get properly quoted. Please see [examples](mssqlExamples.md) for details. This method is useful if you want just a single row and don't want or don't need to iterate over the connection object. This method works in the same way as `iter(conn).next()` to obtain single row. Remaining rows, if any, can still be iterated after calling this method. Example usage:
```
empinfo = conn.execute_row("SELECT * FROM employees WHERE empid=10")
```

  * **`get_header()`** -- This method is infrastructure and don't need to be called by your code. It gets the Python DB-API compliant header information. Returns a list of 7-element tuples describing current result header. Only name and DB-API compliant type is filled, rest of the data is `None`, as permitted by the specs.

  * **`init_procedure(name)`** -- Create an `MSSQLStoredProcedure` object that will be used to invoke stored procedure with given `name`.

  * **`nextresult()`** -- Move to the next result, skipping all pending rows. This method fetches and discards any rows remaining from current operation, then it advances to next result (if any). Returns `True` value if next set is available, `None` otherwise. An exception is raised on failure.

  * **`select_db(dbname)`** -- This function makes given database the current one. An exception is raised on failure.

  * **`__iter__(), next()`** -- These methods faciliate [Python iterator protocol](http://docs.python.org/library/stdtypes.html#iterator-types). You most likely will not call them directly, but indirectly by using iterators.

### MSSQLStoredProcedure class ###

> This class represents a stored procedure. You create an object of this class by calling `init_procedure()` method on `MSSQLConnection` object.

> ### MSSQLStoredProcedure object properties ###

  * **`connection`** -- An underlying MSSQLConnection object.

  * **`name`** -- The name of the procedure that this object represents.

  * **`parameters`** -- The parameters that have been bound to this procedure.

> ### MSSQLStoredProcedure object methods ###

  * **`bind(value, dbtype, name=None, output=False, null=False, max_length=-1)`** -- This method binds a parameter to the stored procedure. `value` and `dbtype` are mandatory arguments, the rest is optional.
    * `value` is the value to store in the parameter,
    * `dbtype` is one of: `SQLBINARY`, `SQLBIT`, `SQLBITN`, `SQLCHAR`, `SQLDATETIME`, `SQLDATETIM4`, `SQLDATETIMN`, `SQLDECIMAL`, `SQLFLT4`, `SQLFLT8`, `SQLFLTN`, `SQLIMAGE`, `SQLINT1`, `SQLINT2`, `SQLINT4`, `SQLINT8`, `SQLINTN`, `SQLMONEY`, `SQLMONEY4`, `SQLMONEYN`, `SQLNUMERIC`, `SQLREAL`, `SQLTEXT`, `SQLVARBINARY`, `SQLVARCHAR`, `SQLUUID`,
    * `name` is the name of the parameter,
    * `output` is the direction of the parameter: `True` indicates that it is also an output parameter that returns value after procedure execution,
    * `null` TBD,
    * `max_length` is the maximum data length for this parameter to be returned from the stored procedure.

  * **`execute()`** -- Execute the stored procedure.

### `_`mssql module exceptions ###

> #### Exception hierarchy ####

```
MSSQLException
|
+-- MSSQLDriverException
|
+-- MSSQLDatabaseException
```

  * **`MSSQLDriverException`** is raised whenever there is a problem within `_mssql` -- e.g. insufficient memory for data structures, and so on.

  * **`MSSQLDatabaseException`** is raised whenever there is a problem with the database -- e.g. query syntax error, invalid object name and so on. In this case you can use the following properties to access details of the error:

  * **`number`** -- The error code, as returned by SQL Server.

  * **`severity`** -- The so-called severity level, as returned by SQL Server. If value of this property is less than the value of `_mssql.min_error_severity`, such errors are ignored and exceptions are not raised.

  * **`state`** -- The third error code, as returned by SQL Server.

  * **`message`** -- The error message, as returned by SQL Server.

> You can find an example of how to use this data at the bottom of [\_mssql examples](mssqlExamples.md) page.