+++
date = "2016-07-06T20:35:39+02:01"
description = "standard error"
title = "stderr"

+++

## STDOUT & STDERR

By just using the option `log` the output file combine the
[standard output and the standard error](https://en.wikipedia.org/wiki/Standard_streams),
in case a file is required per each output the `stderr` could be used,
for example:

```yaml
cmd: command
log:
  file: /var/log/app.log
  age: 86400 # seconds
  num: 7     # int
  size: 1    # MegaBytes
stderr:
  file: /var/log/app-error.log
  age: 86400 # seconds
  num: 7     # int
  size: 1    # MegaBytes
  timestamp: true # will add timesamp to log
```

In this case only the `STDOUT` will be written to `/varlog/app.log` with out
timestamp prefix, and the `STDERR` will be written to `/varlog/app-error.log`
having the timestamp as a prefix.

If only `STDERR` is required this could be used:

```yaml
cmd: command
stderr:
  file: /var/log/app-error.log
  age: 86400 # seconds
  num: 7     # int
  size: 1    # MegaBytes
  timestamp: true # will add timesamp to log
```

By default the `timestamp` is set to false.
