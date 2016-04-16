### pymssql methods ###

  * **`set_max_connections(number)`** -- Sets maximum number of simultaneous database connections allowed to be open at any given time. Default is 25.

  * **`get_max_connections()`** -- Gets current maximum number of simultaneous database connections allowed to be open at any given time.

### Connection <font color='grey'>(pymssqlCnx)</font> class ###

> This class represents an MS SQL database connection. You can create an instance of this class by calling constructor **`pymssql.connect()`**. It accepts the following arguments. Note that in most cases you will want to use keyword arguments, instead of positional arguments.

  * <font color='grey'><b><code>dsn</code></b> -- colon-delimited string of the form <code>host:dbase:user:pass:opt:tty</code>, primarily for compatibility with previous versions of pymssql</font>. This argument is removed in pymssql 2.0.0.

  * **`user`** -- database user to connect as.

  * **`password`** -- user's password.

  * <font color='grey'><b><code>trusted</code></b> -- bolean value signalling whether to use Windows Integrated Authentication to connect instead of SQL autentication with user and password (Windows only)</font> This argument is removed in pymssql 2.0.0 however Windows authentication is still possible.

  * **`host`** -- database host and instance you want to connect to. Valid examples are:
    * `r'.\SQLEXPRESS'` -- SQLEXPRESS instance on local machine (Windows only)
    * `r'(local)\SQLEXPRESS'` -- same as above (Windows only)
    * `r'SQLHOST'` -- default instance at default port (Windows only)
    * `r'SQLHOST'` -- specific instance at specific port set up in `freetds.conf` (Linux/`*`nix only)
    * `r'SQLHOST,1433'` -- specified TCP port at specified host
    * `r'SQLHOST:1433'` -- the same as above
    * `r'SQLHOST,5000'` -- if you have set up an instance to listen on port 5000
    * `r'SQLHOST:5000'` -- the same as above
> '.' (the local host) is assumed if host is not provided.

  * **`database`** -- the database you want initially to connect to, by default SQL Server selects the database which is set as default for specific user.

  * **`timeout`** -- query timeout in seconds, default is 0 (wait indefinitely).

  * **`login_timeout`** -- timeout for connection and login in seconds, default 60.

  * **`charset`** -- character set with which to connect to the database.

  * **`as_dict`** -- whether rows should be returned as dictionaries instead of tuples. You can access columns by 0-based index or by name. Please see [examples](PymssqlExamples.md). (_Added in pymssql 1.0.2_).

  * <font color='grey'><b><code>max_conn</code></b> -- how many simultaneous connections to allow; default is 25, maximum on Windows is 1474559; trying to set it to higher value results in error 'Attempt to set maximum number of DBPROCESSes lower than 1.' (error 10073 severity 7). (<i>Added in pymssql 1.0.2</i>).</font> This property is removed in pymssql 2.0.0.

> ### Connection object properties ###

> This class has no useful properties and data members.

> ### Connection object methods ###

  * **`autocommit(status)`** -- where `status` is a boolean value. This method turns autocommit mode on or off. By default, autocommit mode is off, what means every transaction must be explicitly committed if changed data is to be persisted in the database. You can turn autocommit mode on, what means every single operation commits itself as soon as it succeeds.

  * **`close()`** -- Close the connection.

  * **`cursor()`** -- Return a cursor object, that can be used to make queries and fetch results from the database.

  * **`commit()`** -- Commit current transaction. **You must call this method to persist your data** if you leave `autocommit` at its default value, which is `False`. See also [pymssql examples](PymssqlExamples.md).

  * **`rollback()`** -- Roll back current transaction.

### Cusor <font color='grey'>(pymssqlCursor)</font> class ###

> This class represents a Cursor (in terms of Python DB-API specs) that is used to make queries against the database and obtaining results. You create `pymssqlCursor` instances by calling `cursor()` method on an open `pymssqlCnx` connection object.

> ### Cusor object properties ###

  * **`rowcount`** -- Returns number of rows affected by last operation. In case of SELECT statements it returns meaningful information only after all rows have been fetched.

  * **`connection`** -- This is the extension of the DB-API specification. Returns a reference to the connection object on which the cursor was created.

  * **`lastrowid`** -- This is the extension of the DB-API specification. Returns identity value of last inserted row. If previous operation did not involve inserting a row into a table with identity column, `None` is returned.

  * **`rownumber`** -- This is the extension of the DB-API specification. Returns current 0-based index of the cursor in the result set.

> ### Cusor object methods ###

  * **`close()`** -- Close the cursor. The cursor is unusable from this point.

  * **`execute(operation)`**,
  * **`execute(operation, params)`** -- `operation` is a string and `params`, if specified, is a simple value, a tuple, or `None`. Performs the operation against the database, possibly replacing parameter placeholders with provided values. This should be preferred method of creating SQL commands, instead of concatenating strings manually, what makes a potential of [SQL Injection attacks](http://en.wikipedia.org/wiki/SQL_injection). This method accepts the same formatting as Python's builtin [string interpolation operator](http://docs.python.org/library/stdtypes.html#string-formatting). If you call `execute()` with one argument, the `%` sign loses its special meaning, so you can use it as usual in your query string, for example in `LIKE` operator. See the [examples](PymssqlExamples.md). You **must call** `connection.commit()` after `execute()` or your data will not be persisted in the database. You can also set `connection.autocommit` if you want it to be done automatically. This behaviour is required by DB-API, if you don't like it, just use the `_`mssql module instead.

  * **`executemany(operation, params_seq)`** -- operation is a string and `params_seq` is a sequence of tuples (e.g. a list). Execute a database operation repeatedly for each element in parameter sequence.

  * **`fetchone()`** -- Fetch the next row of a query result, returning a tuple, or a dictionary if `as_dict` was passed to `pymssql.connect()`, or `None` if no more data is available. Raises `OperationalError` if previous call to `execute*()` did not produce any result set or no call was issued yet.

  * **`fetchmany(size=None)`** -- Fetch the next batch of rows of a query result, returning a list of tuples, or a list of dictionaries if `as_dict` was passed to `pymssql.connect()`, or an empty list if no more data is available. You can adjust the batch size using the `size` parameter, which is preserved across many calls to this method. Raises `OperationalError` if previous call to `execute*()` did not produce any result set or no call was issued yet.

  * **`fetchall()`** -- Fetch all remaining rows of a query result, returning a list of tuples, or a list of dictionaries if `as_dict` was passed to `pymssql.connect()`, or an empty list if no more data is available. Raises `OperationalError` if previous call to `execute*()` did not produce any result set or no call was issued yet.

  * <font color='grey'><b><code>fetchone_asdict()</code></b> -- (<i>Warning: this method is not part of DB-API. This method is deprecated as of pymsssql 1.0.2. It was replaced by <code>as_dict</code> parameter to <code>pymssql.connect()</code></i>). Fetch the next row of a query result, returning a dictionary, or <code>None</code> if no more data is available. Data can be accessed by 0-based numeric column index, or by column name. Raises <code>OperationalError</code> if previous call to <code>execute*()</code> did not produce any result set or no call was issued yet.</font> This method is removed in pymssql 2.0.0.

  * <font color='grey'><b><code>fetchmany_asdict(size=None)</code></b> -- (<i>Warning: this method is not part of DB-API. This method is deprecated as of pymsssql 1.0.2. It was replaced by <code>as_dict</code> parameter to <code>pymssql.connect()</code></i>). Fetch the next batch of rows of a query result, returning a list of dictionaries. An empty list is returned if no more data is available. Data can be accessed by 0-based numeric column index, or by column name. You can adjust the batch size using the <code>size</code> parameter, which is preserved across many calls to this method. Raises <code>OperationalError</code> if previous call to <code>execute*()</code> did not produce any result set or no call was issued yet.</font> This method is removed in pymssql 2.0.0.

  * <font color='grey'><b><code>fetchall_asdict()</code></b> -- (<i>Warning: this method is not part of DB-API. This method is deprecated as of pymsssql 1.0.2. It was replaced by <code>as_dict</code> parameter to <code>pymssql.connect()</code></i>). Fetch all remaining rows of a query result, returning a list of dictionaries. An empty list is returned if no more data is available. Data can be accessed by 0-based numeric column index, or by column name. Raises <code>OperationalError</code> if previous call to <code>execute*()</code> did not produce any result set or no call was issued yet. The idea and original implementation of this method by Sterling Michel <code>&lt;</code>sterlingmichel_at_gmail_dot_com<code>&gt;</code>.</font> Thie method is removed in pymssql 2.0.0.

  * **`nextset()`** -- This method makes the cursor skip to the next available result set, discarding any remaining rows from the current set. Returns `True` value if next result is available, `None` if not.

  * **`__iter__()`**, **`next()`** -- These methods faciliate [Python iterator protocol](http://docs.python.org/library/stdtypes.html#iterator-types). You most likely will not call them directly, but indirectly by using iterators.

  * **`setinputsizes()`**, **`setoutputsize()`** -- These methods do nothing, as permitted by DB-API specs.