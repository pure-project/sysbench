[![Latest Release][release-badge]][release-url]
[![Build Status][travis-badge]][travis-url]
[![Debian Packages][deb-badge]][deb-url]
[![RPM Packages][rpm-badge]][rpm-url]
[![Coverage Status][coveralls-badge]][coveralls-url]
[![License][license-badge]][license-url]

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-generate-toc again -->
**Table of Contents**

- [sysbench](#sysbench)
    - [Features](#features)
- [Building and Installing From Source](#building-and-installing-from-source)
    - [Build Requirements](#build-requirements)
        - [Windows](#windows)
        - [Debian/Ubuntu](#debianubuntu)
        - [RHEL/CentOS](#rhelcentos)
        - [Fedora](#fedora)
        - [macOS](#macos)
    - [Build and Install](#build-and-install)
- [Usage](#usage)
    - [General Syntax](#general-syntax)
    - [General Command Line Options](#general-command-line-options)
    - [Random Numbers Options](#random-numbers-options)
- [Versioning](#versioning)

<!-- markdown-toc end -->

# sysbench-basic

**sysbench-basic is sysbench's fork but remove LuaJIT and SQL.**

sysbench-basic is a multi-threaded benchmark tool. It is most frequently used for machine benchmarks.


sysbench comes with the following bundled benchmarks:

- `fileio`: a filesystem-level benchmark
- `cpu`: a simple CPU benchmark
- `memory`: a memory access benchmark
- `threads`: a thread-based scheduler benchmark
- `mutex`: a POSIX mutex benchmark

## Features

- extensive statistics about rate and latency is available, including
  latency percentiles and histograms;
- low overhead even with thousands of concurrent threads. sysbench is
  capable of generating and tracking hundreds of millions of events per
  second;

# Building and Installing From Source

## Build Requirements

### Windows
Build sysbench on Windows is using
[Windows Subsystem for Linux available in Windows 10](https://msdn.microsoft.com/en-us/commandline/wsl/about).

After installing WSL and getting into bash prompt on Windows, following
Debian/Ubuntu build instructions is sufficient.

### Debian/Ubuntu
``` shell
    apt -y install make automake libtool pkg-config libaio-dev
```

### RHEL/CentOS
``` shell
    yum -y install make automake libtool pkgconfig libaio-devel
```

### Fedora
``` shell
    dnf -y install make automake libtool pkgconfig libaio-devel
```

### macOS

Assuming you have Xcode (or Xcode Command Line Tools) and Homebrew installed:
``` shell
    brew install automake libtool pkg-config
```

## Build and Install
``` shell
    ./autogen.sh
    ./configure
    make -j
    make install
```

# Usage

## General Syntax

The general command line syntax for sysbench is:

		  sysbench [options]... [testname] [command] 

- *testname* is an optional name of a built-in test (e.g. `fileio`,
  `memory`, `cpu`, etc.), or a name of one of the bundled Lua scripts
  (e.g. `oltp_read_only`), or a *path* to a custom Lua script. If no
  test name is specified on the command line (and thus, there is no
  *command* too, as in that case it would be parsed as a *testname*), or
  the test name is a dash ("`-`"), then sysbench expects a Lua script to
  execute on its standard input.

- *command* is an optional argument that will be passed by sysbench to
  the built-in test or script specified with *testname*. *command*
  defines the *action* that must be performed by the test. The list of
  available commands depends on a particular test. Some tests also
  implement their own custom commands.

  Below is a description of typical test commands and their purpose:

	+ `prepare`: performs preparative actions for those tests which need
	them, e.g. creating the necessary files on disk for the `fileio`
	test, or filling the test database for database benchmarks.
	+ `run`: runs the actual test specified with the *testname*
    argument. This command is provided by all tests.
	+ `cleanup`: removes temporary data after the test run in those
    tests which create one.
	+ `help`: displays usage information for the test specified with the
	*testname* argument. This includes the full list of commands
	provided by the test, so it should be used to get the available
	commands.

- *options* is a list of zero or more command line options starting with
	`'--'`. As with commands, the `sysbench testname help` command
	should be used to describe available options provided by a
	particular test.

	See [General command line options](README.md#general-command-line-options)
	for a description of general options provided by sysbench itself.


You can use `sysbench --help` to display the general command line syntax
and options.

## General Command Line Options

The table below lists the supported common options, their descriptions and default values:

*Option*              | *Description* | *Default value*
----------------------|---------------|----------------
| `--threads`           | The total number of worker threads to create                                                                                                                                                                                                                                                                                                                                                                                                                            
| `--events`            | Limit for total number of requests. 0 (the default) means no limit                                                                                                                                                                                                                                                                                                                                                                                                      
| `--time`              | Limit for total execution time in seconds. 0 means no limit                                                                                                                                                                                                                                                                                                                                                                                                             
| `--warmup-time`       | Execute events for this many seconds with statistics disabled before the actual benchmark run with statistics enabled. This is useful when you want to exclude the initial period of a benchmark run from statistics. In many benchmarks, the initial period is not representative because CPU/database/page and other caches need some time to warm up                                                                                                                                                                                                                                                                                                  
| `--rate`              | Average transactions rate. The number specifies how many events (transactions) per seconds should be executed by all threads on average. 0 (default) means unlimited rate, i.e. events are executed as fast as possible                                                                                                                                                                                                                                                                 
| `--thread-init-timeout` | Wait time in seconds for worker threads to initialize                                                                                                                                                                                                                                                                                                                                                                                                                  
| `--thread-stack-size` | Size of stack for each thread                                                                                                                                                                                                                                                                                                                                                                                                                                           
| `--report-interval`   | Periodically report intermediate statistics with a specified interval in seconds. Note that statistics produced by this option is per-interval rather than cumulative. 0 disables intermediate reports                                                                                                                                                                                                                                                                  
| `--debug`             | Print more debug info                                                                                                                                                                                                                                                                                                                                                                                                                                                   
| `--validate`          | Perform validation of test results where possible                                                                                                                                                                                                                                                                                                                                                                                                                       
| `--help`              | Print help on general syntax or on a specified test, and exit                                                                                                                                                                                                                                                                                                                                                                                                           
| `--verbosity`         | Verbosity level (0 - only critical messages, 5 - debug)                                                                                                                                                                                                                                                                                                                                                                                                                 
| `--percentile`        | sysbench measures execution times for all processed requests to display statistical information like minimal, average and maximum execution time. For most benchmarks it is also useful to know a request execution time value matching some percentile (e.g. 95% percentile means we should drop 5% of the most long requests and choose the maximal value from the remaining ones). This option allows to specify a percentile rank of query execution times to count 

Note that numerical values for all *size* options (like `--thread-stack-size` in this table) may be specified by appending the corresponding multiplicative suffix (K for kilobytes, M for megabytes, G for gigabytes and T for terabytes).

## Random Numbers Options

sysbench provides a number of algorithms to generate random numbers that are distributed according to a given probability distribution. The table below lists options that can be used to control those algorithms.

*Option*              | *Description* | *Default value*
----------------------|---------------|----------------
`--rand-type` | random numbers distribution {uniform, gaussian, special, pareto, zipfian} to use by default. Benchmark scripts may choose to use either the default distribution, or specify it explictly, i.e. override the default. | special
`--rand-seed` | seed for random number generator. When 0, the current time is used as an RNG seed. | 0
`--rand-spec-iter` | number of iterations for the special distribution | 12
`--rand-spec-pct` | percentage of the entire range where 'special' values will fall in the special distribution | 1
`--rand-spec-res` | percentage of 'special' values to use for the special distribution | 75
`--rand-pareto-h` | shape parameter for the Pareto distribution | 0.2
`--rand-zipfian-exp` | shape parameter (theta) for the Zipfian distribution | 0.8

# Versioning

For transparency and insight into its release cycle, and for striving to maintain backward compatibility, sysbench will be maintained under the Semantic Versioning guidelines as much as possible.

Releases will be numbered with the following format:

`<major>.<minor>.<patch>-basic`

And constructed with the following guidelines:

* Breaking backward compatibility bumps the major (and resets the minor and patch)
* New additions without breaking backward compatibility bumps the minor (and resets the patch)
* Bug fixes and misc changes bumps the patch

For more information on SemVer, please visit [http://semver.org/](http://semver.org/).

[coveralls-badge]: https://coveralls.io/repos/github/akopytov/sysbench/badge.svg?branch=master
[coveralls-url]: https://coveralls.io/github/akopytov/sysbench?branch=master
[travis-badge]: https://travis-ci.org/akopytov/sysbench.svg?branch=master
[travis-url]: https://travis-ci.org/akopytov/sysbench?branch=master
[license-badge]: https://img.shields.io/badge/license-GPLv2-blue.svg
[license-url]: COPYING
[release-badge]: https://img.shields.io/github/release/akopytov/sysbench.svg
[release-url]: https://github.com/akopytov/sysbench/releases/latest
[deb-badge]: https://img.shields.io/badge/Packages-Debian-red.svg?style=flat
[deb-url]: https://packagecloud.io/akopytov/sysbench?filter=debs
[rpm-badge]: https://img.shields.io/badge/Packages-RPM-blue.svg?style=flat
[rpm-url]: https://packagecloud.io/akopytov/sysbench?filter=rpms
