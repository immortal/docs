+++
date = "2010-03-24T23:34:54+02:00"
description = "man immortalctl(8)"
title = "immortalctl"

+++

`immortalctl` control services started by `immortal`

    immortalctl [options] [-12achikinouqstvw] [*|service]


`immortalctl` by default prints the status of any available processes started by [immortal](/post/immortal), it first searches for user process located in `$HOME/.immortal` and next on the system global services started by [immortaldir](/post/immortal) located by default in `/var/run/immortal` unless this has been changed by defining the environment var `IMMORTAL_SDIR`.

When no services found the output will be a single row only with the headers:

```text
PID   Up   Down   Name   CMD
```

**PID**    Print the current pid of the service that is been supervised

**Up**     Display the time the application has been running and in color green

**Down**   Display the time the application has been down and in color red, when in color yellow means that `immortal` is following a pid, see `immortal` option `-f`.

**Name**   If the service has been started with [immortaldir](/post/immortaldir) it will be a string matching the name of  the  service.yml file, if the service has been run from command line calling directly `immortal` it will be a PID

**CMD**    The command that is been supervised

By queering the socket of the service directly, extra information can be obtained in JSON format, for example when using curl:

    curl --unix-socket immortal.sock http:/status -s | jq

Will output something like:

```json
{
    "pid": 7713,
    "up": "4.2s",
    "cmd": "sleep 5",
    "fpid": false,
    "count": 4
}
```

**fpid**  True when following a pid.

**count** The number of times the service has been restarted, if this number is to high something maybe wrong with your service since is dying frequently.

An option or signal can be sent to all the services by using a `*` instead of the service name.

# Options

**`exit`**
Exits  supervisor,  when following a PID it will only exit the supervisor, normally this option should not be used since the required behavior is to keep the service running forever.

**`halt`**
Stop the service by sending a TERM signal and exist the supervisor, if using immortaldir(8) the service will be restarted, first will be stooped and immortaldir(8) will start it up again.

**`once`**
If the service is not running, it will be started, if for some reason the  process  stops,  it  wont  be starte again.

**`start`**
Start the service.

**`status`**
Print status.

**`stop`**
Stop the service by sending a TERM signal, will display the down time in the Down column when printing the status.

## Signals

When sending signals, options start with one dash:

    -1        USR1
    -2        USR2
    -a        ALRM
    -c        CONT
    -h        HUP
    -i        INT
    -k        KILL
    -in       TTIN
    -ou       TTOU
    -q        QUIT
    -s        STOP
    -t        TERM
    -w        WINCH

> -v will print the current version

# Environment

**`IMMORTAL_SDIR`** This environment variable allows to override the default
supervise directory /var/run/immortal, used also by [immortal](/post/immortal)
and [immortaldir](/post/immortaldir)

# Examples

Send signal USR2:

```sh
immortalctl -2 service
```

Stop  all  services,  normally  this is not the required behavior, since will exit the supervisor and terminate all services:

```sh
immortalctl halt "*"
```
