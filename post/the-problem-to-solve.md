+++
date = "2016-07-20T21:07:15+02:00"
description = "follow PID, logging, require"
title = "The problem to solve"

+++

## A common way to start up services

Either you are using macOs, FreeBSD, or any Linux variant the idea is to
simplify how process/services are keep up and running, without need to worry
about special environments or paths.

A single [`run.yml`](/post/run.yml) file should be enough for start and supervise
the services, or used as a template within automation tools like
[Saltstack](http://saltstack.org), [Ansible](https://ansible.com), etc.

## Follow PID

In some cases it is required to supervise applications that
daemonize by default. Normally this kind of applications create a
[pidfile](https://en.wikipedia.org/wiki/Process_identifier#Pidfile) every time
they [fork](https://en.wikipedia.org/wiki/Fork_(system_call)), the one can be
used to follow subsequent forks and avoid creating a race condition between
the supervisor trying to start again the process and the forks created by the
application.

Example of a race condition while trying to supervise unicorn with runit:

{{< asciinema 80371 >}}

In the example given below, after forking (``kill -USR2 $PID``), The supervisor in use
``runit`` in this case, tries to start up again the application, since the
process that was supervising died, but instead enters into loop
because the application still up and running using the same port.

## logging
When working in distributed "cloud" environments, centralized logging becomes a
requirement, therefore it is desired to have availability to chose how and where
to log, either to a local file, to a local file and to a remote server or only
remotely either TCP or UDP.

To achive this, **immortal** is not only capable to write logs to a defined
file, but is also allows to specify an external logger, by specifying the ``-l``
and ``-logger`` options example:

```bash
$ immortal -l /tmp/test.log -logger "logger -t test" my-app
```

In this case ``-loger`` is going to pass the Stander output/error to the
[logger](https://www.freebsd.org/cgi/man.cgi?logger(1))

Example logging to a file and to the system syslog using ``logger``:

{{< asciinema 80376 >}}


## Service dependencies

By defining a `require` option the service will only start if all the services defined are running.

```text
/usr/local/etc/immortal
|--foo.yml
|--bar.yml
`--www.yml
```

if `www.yml` contains:

```yaml
require:
  - foo
  - bar
```

If service `foo` and  `bar` are not running, the service will not start.
