---
published: true
title: Use soname to do libraries versioning
layout: post
---
There are three kinds of names you should clarify when linking.  

1. real name

    The filename containing the actual library code.

2. soname

    The soname has the prefix `lib`, the name of the library, the phrase `.so`, followed by a period and a version number that is incremented whenever the interface changes.

3. linker name

    The name that the compiler uses when requesting a library.

soname is used by ldconfig to do versioning of libraries.  ldconfig will create symbolic links to real names of libraries.

> When you install a new version of a library, you install it in one of a few special directories and then run the program ldconfig(8).  ldconfig examines the existing files and creates the sonames as symbolic links to the real names, as well as setting uphe cache file /etc/ld.so.cache.

Example:

    gcc -c -fPIC sum.c
    gcc -shared -Wl,-soname,libsum.so.1 -o libsum.so.1.0 sum.o -lc
    ldconfig -n .
    ls -l libsum.so.1
    lrwxrwxrwx 1 kai kai   13 Jul 25 17:35 libsum.so.1 -> libsum.so.1.0

    # If there is another version of sum function, we increase the minor version number by 1.
    # However, we keep the soname the same as the previous version.
    gcc -c -fPIC sum2.c
    gcc -shared -Wl,-soname,libsum.so.1 -o libsum.so.1.1 sum2.o -lc
    ldconfig -n .
    ls -l libsum.so.1
    lrwxrwxrwx 1 kai kai   13 Jul 25 17:36 libsum.so.1 -> libsum.so.1.1

Reference:

[Program Library HOWTO] : http://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html