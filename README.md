# Acropolis

The [Research & Education Space](https://bbcarchdev.github.io/res/) software stack.

[![Current build status][travis]](https://travis-ci.org/bbcarchdev/acropolis)
[![Apache 2.0 licensed][license]](#license)
![Implemented in C][language]
[![Follow @RES_Project][twitter]](https://twitter.com/RES_Project)

This “umbrella” project has been assembled to make it easier to maintain and
run tests on the all of the individual components which make up the Acropolis
stack.

This software was developed as part of the [Research & Education Space project](https://bbcarchdev.github.io/res/) and is actively maintained by a development team within BBC Design and Engineering. We hope you’ll find this project useful!

## Table of Contents

* [Requirements](#requirements)
* [Using Acropolis](#using-acropolis)
* [Bugs and feature requests](#bugs-and-feature-requests)
* [Building from source](#building-from-source)
* [Automated builds](#automated-builds)
* [Contributing](#contributing)
* [Information for BBC Staff](#information-for-bbc-staff)
* [License](#license)

## Requirements

In order to build Acropolis in its entirety, you will need:—

* A working build environment, including a C compiler such as [Clang](https://clang.llvm.org), along with [Autoconf](https://www.gnu.org/software/autoconf/), [Automake](https://www.gnu.org/software/automake/) and [Libtool](https://www.gnu.org/software/libtool/)
* The [Jansson](http://www.digip.org/jansson/) JSON library
* [libfcgi](http://www.fastcgi.com)
* The client libraries for [PostgreSQL](https://www.postgresql.org) and/or [MySQL](https://dev.mysql.com/downloads/); it may be possible to use [MariaDB](https://mariadb.org)’s libraries in place of MySQL’s, but this is untested
* [libcurl](https://curl.haxx.se/libcurl/)
* [libxml2](http://xmlsoft.org/)
* The [Redland](http://librdf.org) RDF library (a more up-to-date version than your operating system provides may be required for some components to function correctly)

Optionally, you may also wish to install:—

* [Qpid Proton](https://qpid.apache.org/releases/qpid-proton-0.17.0/), if you wish to use AMQP messaging
* [CUnit](http://cunit.sourceforge.net/), to be able to compile some of the tests
* [`xsltproc`](http://xmlsoft.org/xslt/) and the [DocBook 5 XSL Stylesheets](http://wiki.docbook.org/DocBookXslStylesheets)

On a Debian-based system, the following should install all of the necessary
dependencies:

    $ sudo apt-get install -qq libjansson-dev libmysqlclient-dev libpq-dev libqpid-proton-dev libcurl4-gnutls-dev libxml2-dev librdf0-dev libltdl-dev uuid-dev libfcgi-dev automake autoconf libtool pkg-config libcunit1-ncurses-dev build-essential clang xsltproc docbook-xsl-ns

Acropolis has not yet been ported to non-Unix-like environments, and will install
as shared libraries and on macOS rather than a framework.

Much of it ought to build inside Cygwin on Windows, but this is untested.

[Contributions](#contributing) for building properly with Visual Studio or
Xcode, and so on, are welcome (provided they do not significantly
complicate the standard build logic).

## Using Acropolis

Acropolis consists of a number of different individual components, including
libraries, command-line tools, web-based servers, and back-end daemons. They
are:—

* [liburi](https://github.com/bbcarchdev/liburi): a library for parsing URIs
* [libsql](https://github.com/bbcarchdev/libsql): a library for accessing SQL databases
* [liblod](https://github.com/bbcarchdev/liblod): a library for interacting with [Linked Data](http://linkeddata.org) servers
* [libmq](https://github.com/bbcarchdev/libmq): a library for interacting with message queues
* [libcluster](https://github.com/bbcarchdev/libcluster): a library for implementing load-balancing clusters
* [libawsclient](https://github.com/bbcarchdev/libawsclient): a library for access services which use [Amazon Web Services authentication](http://docs.aws.amazon.com/AmazonS3/latest/dev/RESTAuthentication.html)

* [Anansi](https://github.com/bbcarchdev/anansi): a web crawler
* [Twine](https://github.com/bbcarchdev/twine): an RDF workflow engine
* [Quilt](https://github.com/bbcarchdev/quilt): a Linked Data web server (via FastCGI)
* [Spindle](https://github.com/bbcarchdev/spindle): a Linked Data indexing engine

* [`twine-anansi-bridge`](https://github.com/bbcarchdev/twine-anansi-bridge): a plug-in module for Twine which allows it to retrieve resources for processing from Anansi’s cache

Note that this repository exists for development and testing purposes *only*: in
a production environment, each component is packaged and deployed individually.

### Inside Acropolis

Information about the design of the stack, its principles of operation, and how
to use the Research & Education Space can be found in our book for developers
and collection-holders, [Inside Acropolis](https://bbcarchdev.github.io/inside-acropolis/).

The live production API endpoint for the Research & Education Space can
be found at http://acropolis.org.uk/

## Bugs and feature requests

If you’ve found a bug, or have thought of a feature that you would like to
see added, you can [file a new issue](https://github.com/bbcarchdev/acropolis/issues). A member of the development team will triage it and add it to our internal prioritised backlog for development—but in the meantime we [welcome contributions](#contributing) and [encourage forking](https://github.com/bbcarchdev/acropolis/fork).

## Building from source

You will need git, automake, autoconf and libtool. Also see the [Requirements](#requirements)
section.

    $ git clone git://github.com/bbcarchdev/acropolis.git
    $ cd acropolis
    $ git submodule update --init --recursive
    $ autoreconf -i
    $ ./configure --prefix=/some/path --enable-debug
    $ make
    $ make check
    $ sudo make install

If you don’t specify an installation prefix to `./configure`, it will default to `/opt/res`.

**Important**: The Acropolis repository incorporates its various components as
[Git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules), which
means that each subdirectory will point to a specific commit. This allows
us to ensure that a fresh clone of the repository points to a stable set of
commit. 

However, if you are using this tree to modify or maintain Acropolis, you will
probably want to track the `develop` branches in each submodule instead of the
stable commit pointed to by default.

You can do this yourself by invoking `git fetch origin develop:develop && git checkout develop`
in each submodule, or if you have already configured the tree, you can attempt
`make checkout`, which will *attempt* to do the same thing automatically.

### When to re-run `autoreconf`

If any of the following occur, either through your own changes, or because a
`git pull` or `git checkout` caused them, you should re-run `autoreconf -i`
in the affected parts of the tree (directories or parent directories which
contain a `configure.ac`), or from the top-level:—

* Any files in the `m4` subdirectories are modified
* Any `Makefile.am` files are modified
* Any `configure.ac` files are modified
* Any `*.m4` files are modified
* Directories are added or removed

### Re-building part of the tree

The Automake-based build logic is designed to allow you to rebuild almost any part of
the tree whenever you need to: if you are just working on [Quilt](https://github.com/bbcarchdev/quilt),
for example, you may find yourself making and testing changes almost exclusively
within the `quilt` subdirectory for the duration of that work.

If you know your build logic changes are restricted to one particular submodule,
you can change into the submodule directory and run the following:

    quilt $ autoreconf -i && ./config.status --recheck && make clean
    quilt $ make

## Automated builds

We have configured [Travis](https://travis-ci.org/bbcarchdev/acropolis) to automatically build and invoke the tests on the stack for new commits on each branch. See [`.travis.yml`](.travis.yml) for the details.

You may wish to do similar for your own forks, if you intend to maintain them.

## Contributing

If you’d like to contribute to Acropolis, [fork this repository](https://github.com/bbcarchdev/acropolis/fork) and commit your changes to the
`develop` branch.

For larger changes, you should create a feature branch with
a meaningful name, for example one derived from the [issue number](https://github.com/bbcarchdev/acropolis/issues/).

Once you are satisfied with your contribution, open a pull request and describe
the changes you’ve made and a member of the development team will take a look.

## Information for BBC Staff

This is an open source project which is actively maintained and developed
by a team within Design and Engineering. Please bear in mind the following:—

* Bugs and feature requests **must** be filed in [GitHub Issues](https://github.com/bbcarchdev/acropolis/issues): this is the authoratitive list of backlog tasks.
* Issues with the label [triaged](https://github.com/bbcarchdev/acropolis/issues?q=is%3Aopen+is%3Aissue+label%3Atriaged) have been prioritised and added to the team’s internal backlog for development. Feel free to comment on the GitHub Issue in either case!
* You should never add nor remove the *triaged* label to yours or anybody else’s Github Issues.
* [Forking](https://github.com/bbcarchdev/acropolis/fork) is encouraged! See the “[Contributing](#contributing)” section.
* Under **no** circumstances may you commit directly to this repository, even if you have push permission in GitHub.
* If you’re joining the development team, contact *“Archive Development Operations”* in the GAL to request access to GitLab (although your line manager should have done this for you in advance).

Finally, thanks for taking a look at this project! We hope it’ll be useful, do get in touch with us if we can help with anything (*“RES-BBC”* in the GAL, and we have staff in BC and PQ).

## License

Copyright © 2017 [BBC](http://www.bbc.co.uk/)

The majority of the Acropolis stack is licensed under the terms of the
[Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0).

However, see the documentation within individual submodules for exceptions
to this and information about third-party components which have been
incorporated into the tree.

[travis]: https://img.shields.io/travis/bbcarchdev/acropolis.svg
[license]: https://img.shields.io/badge/license-Apache%202.0-blue.svg
[language]: https://img.shields.io/badge/implemented%20in-C-yellow.svg 
[twitter]: https://img.shields.io/twitter/url/http/shields.io.svg?style=social&label=Follow%20@RES_Project
