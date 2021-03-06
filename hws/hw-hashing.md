ICS: Programming HW: Hashes
===========================

[Go up to the ICS HW page](index.html) ([md](index.md))

You will want to see the
[homeworks policies page](../uva/hw-policies.html)
([md](../uva/hw-policies.md)) for formatting and other details.  The
due dates are listed on the [UVa course page](../uva/index.html)
([md](../uva/index.md)).

The reference platform for this assignment will be 64-bit Ubuntu Linux, version 18.04.  The VirtualBox image (see the Collab landing page for details) is configured for this.

### Purpose

In this assignment, you will be examining some of the issues surrounding hashes and their security applications.

Note that due to the time issues related to task 2, there will be no extensions granted on this assignment.  Task 2 will take some hours of the computer thinking, and if you wait to the last minute to start, you will end up with a late assignment.


### Task 1: CRC insecurity

Your job is to write a C/C++ program (necessary for speed reasons) that, when given an input file and a CRC checksum, will modify that message, and ensure that the modified version matches the CRC checksum.

For this program, you are to use the CRC library in [Boost](https://en.wikipedia.org/wiki/Boost_(C%2B%2B_libraries)). Boost is a C++ library somewhat similar to Java's SDK.  Details about the Boost library can be found [here](http://www.boost.org/doc/libs/1_68_0/libs/crc/crc.html); that page includes a sample program.  Also see [here](http://stackoverflow.com/questions/2573726/how-to-use-boostcrc) for another example as to how to use the CRC functions.

Note that all Boost files have an .hpp extension, and are #include'd like the STL library - meaning you don't have to compile them separately (there are some exceptions to this rule, but not ones we will see for this assignment).  You will need three Boost files: [crc.hpp](http://www.boost.org/doc/libs/1_68_0/boost/crc.hpp), [cstdint.hpp](http://www.boost.org/doc/libs/1_68_0/boost/cstdint.hpp), and [config.hpp](http://www.boost.org/doc/libs/1_68_0/boost/config.hpp). You can download the files from those links if you don't have them; if you have Boost installed, you can use those as well.

You should NOT submit the .hpp files (we have copies as well).  Your program should include the files as follows:

```
#include <boost/crc.hpp>
#include <boost/cstdint.hpp>
```

Note that the Linux VirtualBox image does not have Boost installed, but you can install it via `sudo apt-get install libboost-dev libboost-doc`.  (If that runs into errors, try running `sudo apt-get update` first, then the install command.)  Alternatively, you can compile the code with the files linked to above (use `#include "crc.hpp"` for development), and just change the #includes over before submission.  You don't need any other files in the Boost library for this assignment, so just copying those files is fine for this.

Your program should be called `crc.cpp` and be compiled as `crc` (see the Makefile section, below, for details).  It will be run with two command-line parameters:

- the input file name to read from
- the desired CRC value (in hex) - this will be 4 hexadecimal characters, such as 'abcd' (we will leave out the leading '0x')

A sample execution:

```
./crc input.txt abcd
```

The program should write its output to a file named `output.txt`, which should contain the following:

- The contents of the original file in their entirety (it will only be printable ASCII characters)
- A message of your own (make it something witty, funny, or otherwise interesting for us to read).
- A reasonable amout of PRINTABLE ASCII characters (decimal values 32 - 127) to the end of the input file (reasonable means 10 or less) so that the new output file will have the same CRC as the passed desired CRC value.

Note that there are differences in text files between Linux and Windows platforms (see [here](http://en.wikipedia.org/wiki/Newline) for details); this program will be run in (and graded in) a Linux environment.

Adding the message of your own shows that you can "modify" the original message without having to deal with parsing of the original message.

To make this program reasonable to run in a short time frame, we are going to be using the CRC16 algorithm, NOT the CRC32 algorithm.  Boost can do both.  You can install the `crc32` binary in Ubuntu by installing the libarchive-zip-perl package (`sudo apt-get install libarchive-zip-perl`).  First make sure you are computing the CRC32 hash properly with your code and the `crc32` program (follow the directions linked to above).  Once you can do that, change it over to CRC16 (Boost provides both CRC16 and CRC32 data types and functions).

To determine the CRC16 of a given string, see [here](https://www.lammertbies.nl/comm/info/crc-calculation.html).  Note that the string entered will NOT have a trailing newline (`\n`), so if you put it into a file with a trailing newline, it will come up with a different CRC16 result.  You can put a string into a text file, then remove the trailing newline via a method [here](https://stackoverflow.com/questions/1654021/how-can-i-delete-a-newline-if-it-is-the-last-character-in-a-file).  The CRC16 for "hello world" with no trailing newline is 0x39c1; with a trailing newline is 0x9778 (note that this is a trailing newline in a UNIX-based system, so the file is 12 bytes; on a Windows system, it will be different, since Windows handles newlines differently).

A few hints:

- You need to create a new `crc_16_type` result EACH time you compute the CRC value; you can't re-use it very easily
- Your program will be given 60 seconds to run when we are grading it.  Since we are only dealing with CRC16, this should be enough time.  You may want to include the `-O2` compilation flag, though.

Your program source code must be in a `crc.cpp` file, and the binary must be called `crc`.  See the Makefile section, below.


### Task 2: MD5 collisions

How easy is it to create a malicious program with a specific MD5 hash?  In this part we'll find out.

For this task, we are going to follow the instructions found online [here](http://www.mscs.dal.ca/~selinger/md5collision/).  This code is released under the Modified BSD and/or the GPL license, so I am allowed to use it here, as long as I don't claim credit for it (I'm not), and I include the license in the source code (it's included there).

***WARNING:*** While I have tried this on my computer, I can make no guarantees about the safety of the program.  Thus, you should run it in the Linux VirtualBox image just to be safe.  That being said, I didn't, and the world hasn't ended yet.  For the VirtualBox image, and instructions how to install it, see the main Collab landing page.

You can download the source code from that website or from Collab; the file is called `evilize-0.2.tar.gz` or `evilize-0.2.zip`.

Your task is to create two binary executables, `good` and `evil`, that have the same MD5 hash.  Those executables should print something relevant (i.e., something "good" and something "evil") - it can be interesting quotations, good/evil instructions, etc.  __IT SHOULD NOT DO ANYTHING MALICIOUS__, as that would be a violation of your [Ethics honor pledge](../uva/ethics-pledge.pdf).  It should print something good/evil, not __do__ something good/evil.  Find some interesting quotations to entertain us!

The binary executables should be Linux binary executables, as we will be running them on a 64 bit Linux system.  As long as they are elf executables (32 bit or 64 bit) created in a Linux environment, you are fine.  If you use any obscure libraries, you should check with me first to make sure that they will run.

For this part, you should submit four files:

- `good`, the first binary with a matching MD5 hash
- `evil`, the second binary with a matching MD5 hash
- `multiple_personalities.c`, the source code file that contains `main_good()` and `main_evil()`, the `main()` functions from the `good` and `evil` executables -- we aren't going to compile this, we just want to look over the source code
- `md5.pdf`, the report for this section (see below)

As the binary files will already have been created, and we don't need to compile your source code file, there are no entries for this task in the Makefile.

The report (in the `md5.pdf`) file should answer the following question: how does this whole thing work?  We aren't looking for a mathematical analysis of how the MD5 collision algorithm works.  Rather, an overview of how the entire evilize and md5coll programs work.  How long this part takes is up to you, but we aren't expecting more than 1 page (and don't bother with double-spacing, we hate that).  If you can explain it adequately in 1/2 page, that's fine.  We are looking for quality of your description, not a specific quantity of words or lines.

Note that it took 90 minutes on my home computer (3.4 Ghz machine) to run; your mileage may vary.  And it can only run on one core, so multi-core machines do not get much of a boost.

Check the MD5 checksums of the files "good" and "evil"; they should be the same. Run the programs "good" and "evil" - they should exhibit the two different behaviors that you programmed in the `main_good()` and `main_evil()` functions.


### Task 3: Miscellaneous write-up

There are a few questions to answer, and they should be in a `misc.pdf` file.  Again, we are not looking for quantity, only quality; each one can reasonably be answered in 4 lines or less (assuming those 4 lines are correct, of course).

- What is the string that has the MD5 hash of abc20d7bde1df257f890e152af2e3470?  How did you determine this?
- What is password salting?  Why would we use it?
- What is a dictionary attack?  When is it used?


### Submission

The files that you need to submit are:

- `crc.cpp` (from task 1)
- `good`, `evil`, `multiple_personalities.c`, and `md5.pdf` (from task 2); note that `good` and `evil` are binary executables
- `misc.pdf` (from task 3)
- `Makefile` (described below)

The Makefile needs to compile `crc.cpp` (from task 1) into a binary executable called `crc`.  It does not need to do anything for tasks 2 and 3. Below is a sample Makefile.  Recall that with Makefiles, you must replace the leading 5 spaces on each target task with a single tab.

```
main:
     g++ -O2 -o crc crc.cpp
```

We will, obviously, be compiling your submission with `make`.
