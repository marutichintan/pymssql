### Compilation and installation from source ###

If you need to compile pymssql, check whether requirements shown below are met, unpack source files to a directory of your choice and issue (as root):
```
# python setup.py install
```
This will compile and install pymssql.

### Build Requirements ###

  * Python language. Please check [platforms](Platforms.md) page for version info.
  * Python development package -- if needed by your OS (for example `python-dev` or `libpython2.5-devel`).
  * Linux, `*`nix and Mac OS X: [FreeTDS](http://www.freetds.org/) 0.63 or newer (you need `freetds-dev` or `freetds-devel` or similar named package -- thanks Scott Barr for pointing that out). _NOTE: FreeTDS must be configured with `--enable-msdblib` to return correct dates! See [FreeTDS and Dates](FreeTDSAndDates.md) for details_.
  * Windows: SQL Developer Tools, to be found on MS SQL 2000 installation media.
  * Windows: If you compile pymssql for Python 2.4 or 2.5, you need either Microsoft Visual C++ .NET 2003 or Microsoft Visual Studio .NET 2003. It will complain if you will try to use newer versions. Unfortunately Microsoft haven't provided free versions of these products,
  * Windows: If you compile pymssql for Python 2.6 or newer, you need Microsoft Visual Studio 2005 or Microsoft Visual Studio 2008. I think downloadable [Microsoft Visual C++ Express Edition](http://www.microsoft.com/express/download/) can also be used.

### Platform-specific issues ###

### Mandriva Linux ###

If you use some older versions of Mandriva Linux and want to compile pymssql, you may have to edit `setup.py` and change:
```
    libraries = ["sybdb"]
```
to:
```
    libraries = ["sybdb_mssql"]
```
Be sure to install `libfreetds_mssql0` package first.

### Windows ###

FreeTDS on Windows is not supported. But this is going to change soon. Our goal is to use FreeTDS on every supported platform.

**Please also consult [FreeTDS and Dates](FreeTDSAndDates.md) document**.