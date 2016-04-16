## Supported Python Versions ##

pymssql works on the following Python versions:

| **pymssql** | **<2.4** | **2.4** | **2.5** | **2.6** | **2.7** | **3.0** | **3.1** |
|:------------|:---------|:--------|:--------|:--------|:--------|:--------|:--------|
| **1.0.x**   | -        | +       | +       | +       | ND      | -       | -       |
| **0.8.x**   | -        | +       | +       | -       | -       | -       | -       |
| **0.7.x**   | +        | +       | -       | -       | -       | -       | -       |

  * pymssql version 0.8.0 and newer use Python 2.4 features, so they don't work with older Python releases
  * there is work in progress to support Python 3.0 and 3.1

## Tested Operating Systems ##

pymssql has been tested or reported to work on the following operating systems. You are welcome to report other platforms.

| **pymssql**   | Windows | Linux   | MacOS | FreeBSD | NetBSD  | Solaris |
|:--------------|:--------|:--------|:------|:--------|:--------|:--------|
| **1.0.x**     | + <sup>5</sup>   | + 32/64 | + <sup>4</sup> | + 32/64 | + 32/64 | ND      |
| **0.8.x** <sup>6</sup> | +       | + 32/64 | ND    | +       | +       | + <sup>1</sup>   |
| **0.7.4** <sup>6</sup> | +       | + 32/64 | + <sup>2</sup> | +       | +       | + <sup>1</sup>   |
| **0.7.1** <sup>6</sup> | +       | + 32/64 | + <sup>3</sup> | +       | +       | + <sup>1</sup>   |

Legend:
  * ND = No Data
  * 32/64 = tested on 32 and 64 bit operating system
  * <sup>1</sup> Solaris 10/x86
  * <sup>2</sup> thanks Sébastien Arnaud for reporting
  * <sup>3</sup> thanks Joseph Kocherhans for reporting
  * <sup>4</sup> thanks MunShik JEONG, and Kurt Sutter for reporting
  * <sup>5</sup> pymssql is available only in 32-bit. You can use it with 32-bit Python on both 32 and 64-bit Windows
  * <sup>6</sup> pymssql 0.8.0 and earlier was tested only with 32-bit operating systems

## Tested SQL Server versions ##

pymssql has been tested or reported to work with the following SQL Server versions. You are welcome to report other versions.

  * SQL Server 2000:
    * Standard Edition,
    * Enterprise Edition,
    * Personal Edition,
    * Developer Edition,
    * MSDE (Desktop Engine)
  * SQL Server 2005:
    * Standard Edition 32- and 64-bit,
    * Express Edition 32- and 64-bit
  * SQL Server 2008:
    * Express Edition 32- and 64-bit

All service pack levels are fine.

Although pymssql works properly in most cases, it has some limitations which you should be aware of. Please consult [Limitations](Limitations.md) page.