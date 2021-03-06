INTRODUCTION
============

This is informixdb 2.5, an Informix implementation of the Python
Database API.  This release implements version 2.0 of the DB-API:

  http://www.python.org/topics/database/DatabaseAPI-2.0.html

The module consists of two components:

- `_informixdb', a Python C extension that interfaces with INFORMIX-ESQL/C
  and implements the DB-API.
- `informixdb', a Python module that wraps around _informixdb and implements
  various helper classes.

It is released under a BSD-like license.  See the COPYRIGHT file
included with this distribution for more details.


INSTALLATION (UNIX)
===================

This distribution uses Python distutils to build and install the informixdb
module.  It requires Python 2.2 or later.

To compile InformixDB, you need to have Informix ESQL/C installed. ESQL/C
is included with the Informix Client Software Development Kit (Informix CSDK),
which is available free of charge at
http://www.ibm.com/software/data/informix/tools/csdk/ .


In a hurry?
-----------

Extract the source distribution, and `cd' to the top-level directory.
Type `python setup.py build_ext' and, as root, `python setup.py install'.

Or want the details?
--------------------

First, extract the source distribution on a machine with Python 2.2 or
later and INFORMIX-ESQL/C, and `cd' to the top-level directory.

Next, run the build script `python setup.py build_ext', giving it any of the
following build options:

  --esql-informixdir=DIR

  Look for INFORMIX-ESQL/C in `DIR'.  If omitted, it defaults to the value
  of the INFORMIXDIR environment variable, or else /usr/informix.

  --esql-static

  Disable the use of Informix shared libraries.  The default is to use
  shared libraries if available.  With this option, you can require
  the Informix static libraries to instead be used.

  --esql-threadlib=LIB

  Enable the use of thread-safe Informix libraries, so that multiple Python
  threads can use informixdb concurrently.

If the build command completes without errors, type `python setup.py install',
which will install the package into the appropriate site-specific library path.


INSTALLATION (Win32)
====================

Windows installers for Python 2.4 and 2.5 are available for download at
http://informixdb.sourceforge.net/. Download the appropriate installer
and run it to install the InformixDB module. Note that you'll need the
Informix Client SDK or Informix Connect to use the InformixDB module.

If you're using a different version of Python, you'll have to compile
InformixDB from the sources, following the same steps as for a Unix
installation. Distutils should do the right thing as long as you have the same
compiler that produced the Python interpreter you're using.


NEWS
====

From 2.4 to 2.5:
- Compatibility with CSDK 3.00
- Ability to manually interrupt or automatically time out SQL execution
- Proper binding of boolean parameters in WHERE clauses
- Make version information about server and client available
- Various bug fixes
 
From 2.3 to 2.4:
- Implement 'named' parameter style to optionally bind query parameters
  by name
- Implement option to retrieve opaque types in their binary representation

From 2.2 to 2.3:
- Allow parameter list for executemany() to be arbitrary iterable objects.
- .prepare() method and .command attribute for explicitly prepared statements
- Python 2.5 compatibility
- Bug fixes
  * Rare crashes caused by missing error check in DESCRIBE step.
  * Inconsistent UDT input binding caused SQLCODE -1820 in bulk insert
    (executemany) if UDT contents went back and forth across 32K size
    boundary or between null and non-null.
  * EXECUTE PROCEDURE erroneously attempted to open a results cursor for
    procedures that don't return results.
  * Date columns were read incorrectly on 64 bit platforms due to mixup
    of int4 versus long.
  
From 2.1 to 2.2:
- Support for BOOLEAN columns
- DECIMAL and MONEY columns can be fetched as decimal.Decimal instances
  if the decimal module is available
- autocommit mode for connections
- Bug fixes:
  * Output buffer allocation used pointer/int casts that don't work on
    most 64 bit platforms.
  * Selecting TEXT/BYTE column from an empty set of rows caused segmentation
    fault under certain circumstances.
  * datetime values with trailing double-zeroes were fetched incorrectly.

From 2.0 to 2.1:
- INTERVAL support
- Scroll cursor and cursor with hold
- Support for Smart Large Objects and User Defined Types

From 1.5 to 2.0:

- Full compliance with DB-API version 2
- Implement LVARCHAR support
- Use insert cursor, if possible, in executemany for improved performance.

From 1.4 to 1.5:

- Further steps towards DB-API 2 compliance:
  * added recommended keyword arguments to connect() method
  * implemented cursor methods/attributes .next(), .executemany(),
    .rowcount, and .arraysize
- informixdb.Error now makes details about the error (such as sqlcode)
  available as attributes.
- sqlerrd wasn't initialized properly, and under many circumstances it
  didn't correspond to the most recent operation. (Specifically,
  it didn't get updated after PREPARE, OPEN, or FETCH.)
  
From 1.3 to 1.4:

- Ported from "Makefile.pre.in" mechanism to distutils.
- Allow passing username and password for connecting to remote databases.
- Expose sqlerrd.
- Trivial baby steps towards DB-API version 2 compliance.

From 1.2 to 1.3:

- Fixed the `fetch{one|many|all}' method output types and/or values
  for several SQL types.  See ChangeLog for more info.

From 1.01 to 1.2:

- `informixdb' now exports `error' as a module global.  It previously
  was only available as a connection attribute.

- The `execute' method for connections now returns a value.  It
  previously returned `None'.

- The `execute' method no longer limits operation strings to 2000
  characters or less.  Strings longer than this used to cause stack
  corruption.

- `None' is mapped to NULL for `execute' method inputs.
  Previously, it was mapped to the literal string "None".

- NULLS are mapped to `None' for `fetch{one|many|all}' method outputs.
  Previously, they were mapped to either zero or an empty string.
  (This fix can be disabled by setting the IFXDB_MASK_NULLS environment
  variable to a non-zero value.  See ChangeLog for more info.)

- Fixed error message formatting.

- Potentially blocking SQL statements are now executed with the Python
  interpreter lock released.  This permits other threads to execute
  while one blocks on a database request.

- Increased the maximum number of rows returned by the `fetchall'
  method from 65536 to `sys.maxint'.

- Fixed a bug that caused raw (i.e., BYTE or TEXT) inputs to
  occasionally be treated as NULLs.

- Fixed a bug that caused datetime outputs with YEAR TO MINUTE
  precision to be wildly inaccurate.

- Reorganized the source files as follows:

  Release 1.0x     Release 1.2
  ---------------  -----------------------
                   README
                   COPYRIGHT
                   ChangeLog
                   configure
                   configure.in
                   Makefile.in
                   install-sh
  informixdb.py    informixdb.py
                   ext/
                       Makefile.pre.in-1.4
                       Setup.in
  ifxdbmodule.ec       _informixdb.ec    
  dbi.c                dbi.c          
  dbi.h                dbi.h
                   old/
  README               README
  Makefile             Makefile
  _informixdb.mak      _informixdb.mak
  _informixdb.def      _informixdb.def

- Fixed numerous memory leaks.

- Added support for thread-safe Informix, so that each thread can open its
  own connections.

- Implemented the "operation caching" feature of the `execute' method,
  as described in the DB-API spec.  If the same operation string is
  passed consecutively to `execute' (perhaps with different inputs
  each time), informixdb now recognizes this condition and uses the
  previously parsed/prepared/described SQL statement.

For complete details, refer to the ChangeLog file included with this
distribution.

See also `old/README' for other NEWS.


NOTES
=====

- If informixdb is built to use Informix shared libraries, you may
  need to set LD_LIBRARY_PATH (or similar environment variable) so that
  the runtime linker can locate these shared libraries.  Otherwise, you
  may get the following exception when importing the informixdb module:

    ImportError: Cannot locate C core module for informix db interface.

- In the operation string argument of the `execute' method, informixdb
  recognizes not only positional parameters (i.e., identified by '?'
  characters) but also ordinal parameters (i.e., identified by ":N"
  substrings, where N is a number from 1 to M corresponding to the Nth
  item in the parameter M-tuple).

See also `old/README' for other NOTES.


FUTURE
======

- Some kind of Type Mapping API
- Some kind of Unicode support
- More unit tests to cover correct functionality in addition to API compliance

MAINTAINER
==========

Got questions and/or problems with informixdb?  Contact me at:
  Carsten Haese <chaese@users.sourceforge.net>

I'd recommend that you also consider CC'ing the Python DB-SIG at:
  db-sig@python.org


ACKNOWLEDGMENTS
===============

Many thanks to Greg Stein and Michael Lorton (late of the eShop) as
the initial authors of informixdb, and to Bertil Reinhammar and Stephen
Turner as its prior maintainers.

Special thanks to Daniel Smertnig for cleaning up the code, implementing
API 2 compliance, and for integrating esql with distutils without the
autoconf crutch.

RELEASE HISTORY
===============

Release  Date        By
-------  ----------  -----------------
1.0      1997-02-19  Bertil Reinhammar
1.01     1997-02-19  Bertil Reinhammar
1.2      1999-05-21  Stephen J. Turner
1.3      1999-11-30  Stephen J. Turner
1.4      2005-04-11  Carsten Haese
1.5      2005-09-18  Carsten Haese
2.0      2005-10-22  Carsten Haese
2.1      2005-11-21  Carsten Haese
2.2      2006-03-26  Carsten Haese
2.3      2006-10-01  Carsten Haese
2.4      2006-12-02  Carsten Haese
2.5      2007-10-16  Carsten Haese

--
Carsten Haese <chaese@users.sourceforge.net>
http://informixdb.sourceforge.net/
