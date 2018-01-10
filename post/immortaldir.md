+++
date = "2010-03-23T23:34:54+02:00"
description = "man immortaldir(8)"
title = "immortaldir"

+++

`immortaldir` start services located in a specified directory.

    immortalctl [dir] [-v]


`immortaldir` will watch for changes in the specified directory
[kqueue(2)](https://www.freebsd.org/cgi/man.cgi?query=kqueue&sektion=2),
if using Linux, will scan the directory every 5 seconds searching for
files ending in `.yml` and track changes. The files should conform the
[immortal](/post/immortal) configuration format.

If a file changes the service will be restarted, `immortalctl exit & stop`.

If a file is touched and service is down, it will be started, `immortalctl start`.

If a file is removed, the supervisor will exit, `immortalctl exit`, this may
end the existing process but in cases where the PID is been followed, only the
supervisor will exit.

Based on your operating system the directory containing the services could be:

    /usr/local/etc/immortal

or

    /etc/immortal

`immortaldir` checks for changes also in the **`IMMORTAL_SDIR`** directory in
order to restart services that are not any more running, for example when a
service has been halted (signal halt) this helps to automatically restart the
service.

> When dealing with multiple operating systems could be handy to use a unique location like `/immortal`

To keep services persistent across reboots a startup script should be used to
run `immortaldir` on boot time.

# Options

**`dir`** Path of directory containing immortal configuration files `*.yml`

**`-v`** Print current version.

# Environment

**`IMMORTAL_SDIR`** This environment variable allows to override the default
supervise directory `/var/run/immortal`, used also by `immortalctl` and
`immortal`.  By default `immortaldir` after finding a configuration file
`service.yml` will invoke `immortal` and pass to it the option:

    -ctl <service>

This will create the  directory "service" in `/var/run/immortal`:

    /var/run/immortal/service
                     |--lock
                     `--immortal.sock

**`lock`** is a file that prevents running more than once the same service

**`immortal.sock`** is a Unix socket that can be used to manage the process, example:

Get status:

    curl --unix-socket immortal.sock http:/status -s

Down. If the service is running, send it a TERM signal. After it stops, do not restart it:

    curl --unix-socket immortal.sock http://im/signal/down -s

Start:

    curl --unix-socket immortal.sock http://im/signal/start -s


If a different path instead of `/var/run/immortal` is required, the `IMMORTAL_SDIR` environment variable can be used.

# Examples

Read configuration files from `/etc/immortal`:

    immortaldir /etc/immortal

Read configuration files from `/srv/immortal` and create supervise directories on `/var/www/immortal`:

```bash
export IMMORTAL_SDIR=/var/www/immortal
immortaldir /srv/immortal
```
