SSL out of buffer access
========================

Project curl Security Advisory, November 29th 2017 -
[Permalink](https://curl.haxx.se/docs/adv_2017-af0a.html)

VULNERABILITY
-------------

libcurl contains an out boundary access flaw in SSL related code.

When allocating memory for a connection (the internal struct called
`connectdata`), a certain amount of memory is allocated at the end of the
struct to be used for SSL related structs. Those structs are used by the
particular SSL library libcurl is built to use. The application can also tell
libcurl which specific SSL library to use if it was built to support more than
one.

The math used to calculate the extra memory amount necessary for the SSL
library was wrong on 32 bit systems, which made the allocated memory too small
by 4 bytes. The last struct member of the last object within the memory area
could then be outside of what was allocated. Accessing that member could lead
to a crash or other undefined behaviors depending on what memory that is
present there and how the particular SSL library decides to act on that memory
content.

Specifically the vulnerability is present if libcurl was built so that
`sizeof(long long *) < sizeof(long long)` which as far as we are aware only
happens in 32-bit builds.

We are not aware of any exploit of this flaw.

INFO
----

This bug was introduced in commit
[70f1db321a](https://github.com/curl/curl/commit/70f1db321a), July 2017.

The Common Vulnerabilities and Exposures (CVE) project has assigned the name
CVE-2017-8818 to this issue.

AFFECTED VERSIONS
-----------------

This is only an issue on systems with 32 bit pointers. (Technically, on
systems where `sizeof(long long *) < sizeof(long long)`.)

- Affected versions: libcurl 7.56.0 to and including 7.56.1
- Not affected versions: libcurl < 7.56.0 and >= 7.57.0

curl is used by many applications, but not always advertised as such.

THE SOLUTION
------------

In libcurl version 7.57.0, the allocation size is corrected.

A [patch for CVE-2017-8818](https://curl.haxx.se/CVE-2017-8818.patch) is
available.

RECOMMENDATIONS
---------------

We suggest you take one of the following actions immediately, in order of
preference:

 A - Upgrade curl to version 7.57.0

 B - Apply the patch to your version and rebuild

TIME LINE
---------

It was reported to the curl project on November 18, 2017.  We contacted
distros@openwall on November 24.

curl 7.57.0 was released on November 29 2017, coordinated with the publication
of this advisory.

(The [original report](https://github.com/curl/curl/issues/2093) was made in public)

CREDITS
-------

Reported by John Schoenick. Patch by Ray Satiro.

Thanks a lot!
