======
bitrot
======

Detects bit rotten files on the hard drive to save your precious photo
and music collection from slow decay.

Usage
-----

Go to the desired directory and simply invoke::

  $ bitrot

This will start digging through your directory structure recursively
indexing all files found. The index is stored in a ``.bitrot.db`` file
which is a SQLite 3 database.

Next time you run ``bitrot`` it will add new files and update the index
for files with a changed modification date. Most importantly however, it
will report all errors, e.g. files that changed on the hard drive but
still have the same modification date.

All paths stored in ``.bitrot.db`` are relative so it's safe to rescan
a folder after moving it to another drive.

Performance
-----------

Obviously depends on how fast the underlying drive is. Since bandwidth
for checksum calculations is greater than your drive's data transfer
rate, even when comparing mobile CPUs vs. SSD drives, the script is
single-threaded.

No rigorous performance tests have been done.  Scanning a ~1000 files
totalling ~4 GB takes 20 seconds on a 2015 Macbook Air (SM0256G SSD).
This is with cold disk cache.

Some other tests back from 2013: a typical 5400 RPM laptop hard drive
scanning a 60+ GB music library took around 15 minutes. On an OCZ
Vertex 3 SSD drive ``bitrot`` was able to scan a 100 GB Aperture library
in under 10 minutes. Both tests on HFS+.

If you'd like to contribute some more rigorous eenchmarks or any
performance improvements, I'm accepting pull requests! :)

Change Log
----------
0.9.5
~~~~~
* Added option to normalize all path names

0.9.4
~~~~~
* Added better progress bar (pip3 install progressbar2)
* Can now specify source and destination directories
0.9.3
~~~~~
* Added option to ignore date modified (only checks hashes). Great for verifying backups for integrity (File Integrity Monitoring) using -t 2 or --test 2
* Added option to allow testing of only recent (default: last 1 day) of recently modified data (great for checking a backup you just synced for integrity) using -r or --recent
* Added database vacuuming to shrink DB size on hard drive of old hashes that went missing
* Added logging to file using -g or ---log (on by default)
* Added email support for hash mismatch using -e or --email (on by default)
* Added a time elapsed counter
* Can now fix files that have invalid modification date, and rename files/dirs that have bad chars in name, using -f or --fix (dangerous)
* Total size now printed in B, KB, MB, GB, TB
* Can now include and exclude at same time, and fixed logic. Exclude takes prescendence
* Added ability to specify hash function from command line. I found SHA512 to be just as fast as SHA1 on my machine using -a or --hashing-function
* Fixed bug when file doesn't have a valid modification timestamp
* Can now create MD5 or SFV files using -c or --sfv
* Now prints out ignored files with verbosity level 4
* Fixes for invalid characters in file names
* Integrates benshep's and liloman's latest changes
* Better warning printing
* Added grammar fixes


0.9.2
~~~~~

* bugfix: one place in the code incorrectly hardcoded UTF-8 as the
  filesystem encoding

0.9.1
~~~~~

* bugfix: print the path that failed to decode with FSENCODING

* bugfix: when using -q, don't hide warnings about files that can't be
  statted or read

* bugfix: -s is no longer broken on Python 3

0.9.0
~~~~~

* bugfix: bitrot.db checksum checking messages now obey --quiet

* Python 3 compatibility

0.8.0
~~~~~

* bitrot now keeps track of its own database's bitrot by storing
  a checksum of .bitrot.db in .bitrot.sha512

* bugfix: now properly uses the filesystem encoding to decode file names
  for use with the .bitrotdb database. Report and original patch by
  pallinger.

0.7.1
~~~~~

* bugfix: SHA1 computation now works correctly on Windows; previously
  opened files in text-mode. This fix will change hashes of files
  containing some specific bytes like 0x1A.

0.7.0
~~~~~

* when a file changes or is renamed, the timestamp of the last check is
  updated, too

* bugfix: files that disappeared during the run are now properly ignored

* bugfix: files that are locked or with otherwise denied access are
  skipped. If they were read before, they will be considered "missing"
  in the report.

* bugfix: if there are multiple files with the same content in the
  scanned directory tree, renames are now handled properly for them

* refactored some horrible code to be a little less horrible

0.6.0
~~~~~

* more control over performance with ``--commit-interval`` and
  ``--chunk-size`` command-line arguments

* bugfix: symbolic links are now properly skipped (or can be followed if
  ``--follow-links`` is passed)

* bugfix: files that cannot be opened are now gracefully skipped

* bugfix: fixed a rare division by zero when run in an empty directory

0.5.1
~~~~~

* bugfix: warn about test mode only in test mode

0.5.0
~~~~~

* ``--test`` command-line argument for testing the state without
  updating the database on disk (works for testing databases you don't
  have write access to)

* size of the data read is reported upon finish

* minor performance updates

0.4.0
~~~~~

* renames are now reported as such

* all non-regular files (e.g. symbolic links, pipes, sockets) are now
  skipped

* progress presented in percentage

0.3.0
~~~~~

* ``--sum`` command-line argument for easy comparison of multiple
  databases

0.2.1
~~~~~

* fixed regression from 0.2.0 where new files caused a ``KeyError``
  exception

0.2.0
~~~~~

* ``--verbose`` and ``--quiet`` command-line arguments

* if a file is no longer there, its entry is removed from the database

0.1.0
~~~~~

* First published version.

Authors
-------

Glued together by `Łukasz Langa <mailto:lukasz@langa.pl>`_. Multiple
improvements by `Yang Zhang <mailto:yaaang@gmail.com>`_, `Jean-Louis
Fuchs <mailto:ganwell@fangorn.ch>`_, `Phil Lundrigan
<mailto:philipbl@cs.utah.edu>`_, `Ben Shepherd
<mailto:bjashepherd@gmail.com>`_, and `Peter Hofmann
<mailto:scm@uninformativ.de>`_.
