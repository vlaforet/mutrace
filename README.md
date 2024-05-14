MUTRACE Mutex Tracer
====

## Installation

Install required dependencies.
On Ubuntu/Debian:
```
apt-get update
apt-get install -y binutils-dev automake libiberty-dev
```

This will install mutrace and its required libraries into `/usr/local/{bin,lib}`:
```
./bootstrap.sh
make
make install
```

## Mutrace usage
Mutrace profiles lock contention for you. Just use it as prefix for your usual command line and it will profile mutexes used in all child processes.

```
$ mutrace -h
mutrace 0.2

Usage: mutrace [OPTIONS...] APPLICATION [ARGUMENTS...]

COMMANDS:
  -h, --help                      Show this help

OPTIONS:
      --hash-size=INTEGER         Set size of mutex hash table
      --frames=INTEGER            Set number of frames to show in stack traces
  -d, --debug-info                Make use of debug information in stack traces
      --max=INTEGER               Show this many mutexes at maximum
      --mutex-order=STRING        Order the summary table of mutexes by this
                                  column (see below for valid column names)
      --cond-order=STRING         Order the summary table of condition variables
                                  by this column (see below for valid column
                                  names)

      --wait-min=INTEGER          Only show condition variables that have been
                                  waited on at least this often
      --locked-min=INTEGER        Only show mutexes that have been locked at
                                  least this often
      --owner-changed-min=INTEGER Only show mutexes whose owning thread changed
                                  at least this often
      --contended-min=INTEGER     Only show mutexes which have been contended
                                  at least this often
      --all                       Show all mutexes, overrides the values of the
                                  three values above
  -f, --first-ld                  Set mutrace as the first library in LD_PRELOAD
  -o, --output-file=STRING        Append traces to this file. If NULL traces
                                  are printed to stderr.

  -r, --track-rt                  Track for each mutex if it was accessed from
                                  a realtime thread
      --trap                      Trigger a debugger trap each time a mutex
                                  inconsistency is detected (for use in
                                  conjunction with gdb)

MUTEX ORDER COLUMN NAMES:
  id                              Mutex number
  n-locked                        Total number of times mutex was locked for
                                  writing
  n-read-locked                   Total number of times mutex was locked for
                                  reading
  n-contended                     Total number of times mutex was contended for
                                  writing
  n-read-contended                Total number of times mutex was contended for
                                  reading
  n-owner-changed                 Total number of times mutex ownership changed
  nsec-locked-total               Total time mutex was locked for writing
  nsec-locked-max                 Maximum time mutex was continuously locked for
                                  writing
  nsec-locked-avg                 Average time mutex was continuously locked for
                                  writing
  nsec-read-locked-total          Total time mutex was locked for reading
  nsec-read-locked-max            Maximum time mutex was continuously locked for
                                  reading
  nsec-read-locked-avg            Average time mutex was continuously locked for
                                  reading
  nsec-contended-total            Total time mutex was contended for writing
  nsec-contended-avg              Average time mutex was continuously contended
                                  for writing
  nsec-read-contended-total       Total time mutex was contended for reading
  nsec-read-contended-avg         Average time mutex was continuously contended
                                  for reading

CONDITION VARIABLE ORDER COLUMN NAMES:
  id                              Condition variable number
  n-wait                          Total number of times condition variable was
                                  waited on (inc. timed waits)
  n-signal                        Total number of times condition variable was
                                  signalled
  n-broadcast                     Total number of times condition variable was
                                  broadcasted
  n-wait-contended                Total number of times condition variable was
                                  concurrently waited on by multiple threads
  n-signal-contended              Total number of times condition variable was
                                  signalled with no threads waiting
  nsec-wait-total                 Total time condition variable was waited on
  nsec-wait-max                   Maximum time condition variable was
                                  continuously waited on
  nsec-wait-avg                   Average time condition variable was
                                  continuously waited on
  nsec-wait-contended-total       Total time condition variable was waited on
                                  by multiple threads before being signalled
  nsec-wait-contended-max         Maximum time condition variable was waited on
                                  by multiple threads
  nsec-wait-contended-avg         Average time condition variable was waited on
                                  by multiple threads
```

Use mutrace to trace mutex usage of your application (example with `ls`):
```
# Print traces to stderr
mutrace ls

# Append traces to a file
mutrace -o ~/trace.txt ls
```

# Matrace usage
Matrace traces memory allocation operations in realtime threads for you. It is of no use in applications that do not make use of realtime scheduling.

```
$ matrace -h
mutrace 0.2

Usage: matrace [OPTIONS...] APPLICATION [ARGUMENTS...]

COMMANDS:
  -h, --help                      Show this help

OPTIONS:
      --frames=INTEGER            Set number of frames to show in stack traces
  -d, --debug-info                Make use of debug information in stack traces
```

```
$ matrace myrealtimetool
```

## Notes
For a terse overview what mutrace can do for you, please read the announcement blog story: [http://0pointer.de/blog/projects/mutrace.html](http://0pointer.de/blog/projects/mutrace.html).

Both tools understand a --debug-info switch in which case the backtraces generated will include debugging information such as line numbers and source file names. This is not enabled by default since generating those traces is not always safe in situations where locks are taken or memory allocated as we do it here. YMMV.

Mutrace cannot be used to profile glibc-internal mutexes.

## License
LGPLv3+

### Exception:
backtrace-symbols.c is GPLv2+. Which probably means that using the --debug-info switch for mutrace and matrace might not be legally safe for non-GPL-compatible applications. However, since that module is independantly built into a seperate .so it should still be safe using the profilers without this switch on such software.

## Authors
Lennart Poettering