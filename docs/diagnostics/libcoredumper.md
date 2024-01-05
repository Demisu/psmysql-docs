# Using the libcoredumper

This feature was implemented in *Percona Server for MySQL* 5.7.31-34 and has been tested against the supported operating systems for that version.

|Operating Systems|Versions|
|--- |--- |
|Debian|9, 10|
|Red Hat Enterprise Linux and derivatives|6, 7, 8|
|Ubuntu|16.04, 18.04, 20.04|

The documented moment of a computer when either the computer or an application crashed is a core dump file. Developers examine the dump as one of the tasks when searching for the cause of a failure.

The `libcoredumper` is a free and Open Source fork of `google-coredumper`, enhanced to work on newer Linux versions, and GCC and CLANG.

Test this tool in a safe environment before using in production.

## Enable the libcoredumper

Enable core dumps for troubleshooting purposes.

To enable the `libcoredumper`, add the `coredumper` variable to the `mysqld` section of `my.cnf`. This variable is independent of the older `core-file` variable.

The variable can have the following possible values:

|Value|Description|
|--- |--- |
|Blank|The core dump is saved under MySQL datadir and named `core`.|
|A path ending with `/`|The core dump is saved under the specified directory and named `core`.|
|Full path with a filename|The core dump is saved under the specified directory and with the specified filename|

Restart the server.

## Verify the tool is active

MySQL writes to the log when generating a core file and delegates the core dump operation to the Linux kernel. An example of the log message is the following:

```text
Writing a core file
```

MySQL using the `libcoredumper` to generate the file creates the following message in the log:

```text
Writing a core file using lib coredumper
```

Every core file adds a crash timestamp instead of a PID for the following reasons:

* Correlates the core file with a crash. MySQL prints a UTC timestamp on the crash log.

    > ```text
    > 10:02:09 UTC - mysqld got signal 11;
    > ```

* Keeps multiple core files.

    For example, the operators and containers run as PID 1. If the process ID identified the core file, each container crash generates a core dump that overwrites the previous core file.

### Disable the libcoredumper

You can disable the libcoredumper. A core file may contain sensitive data and takes disk space.

To disable the `libcoredumper`, do the following:

1. In the `mysqld` section of my.cnf, remove the `libcoredumper` variable.

2. Restart the server.