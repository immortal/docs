+++
date = "2016-07-06T20:34:39+02:00"
description = "-r / retries"
title = "retries"
+++

In some cases, you may not want to run a command forever, here is where the
option -r / retries becomes useful.

This option normally is only used directly from the command line, for example:

```sh
immortal -w 60 -r 3 rsync -aHAXxv --numeric-ids --delete -P <src> user@host:/<dest>
```

In this case **immortal** will wait **-w** 60 seconds before starting and it
will only try to run the command for 3 times after that it will exit.

Example using `retries` in a configuration file:

```yaml
cmd: rsync -aHAXxv --numeric-ids --delete -P <src> user@host:/<dest>
log:
    file: /tmp/rsync.log
wait: 60
retries: 3
```

> by default retries is set to -1, runs forever, if set to 0 it will exit after terminating the process

## IMMORTAL_EXIT

Normally the configuration files `run.yml` are used with
[immortaldir(8)](/post/immortaldir), in this case the supervisor will not exit
and instead will just avoid executing the command again, similar to the `once`
option when using [immortalctl(8)](/post/immortalctl).

To change this behavior and to **exit** the supervisor, set the environment
variable `IMMORTAL_EXIT`:

    export IMMORTAL_EXIT=1

> If using `immortaldir` is not advised to set this environment var since may cause a start/stop loop
