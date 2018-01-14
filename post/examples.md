---
title: "Examples"
date:  "2010-03-20T23:10:07+02:00"
description: "service.yml"
---

The main idea behind **immortal** is to keep up and running services forever
even if the server is rebooted the service should be started and based on the
requirements probably the services may start in sequence by waiting for others
to start, wait some seconds before starting, etc, but at the end the idea
remains, "run something forever" and in case exist just restart it.

Doing this could be tricky, mainly because if you just want to launch your
service/worker/daemon, etc you may not want to spend to much time understanding
how to properly launch something in the operating system your company is using.
Probably you just join a new team and when are about to deploy, found out that
the production servers are not using the OS you were used to besides they don't
support/have your known supervisor, have a mix of init/upstart scripts or in the
best case, they are using daemontools/runit.

In order to simplify this process across environments/platforms and try to make
a more straightforward and easy process, it is important to understand how
[immortaldir](/post/immortaldir) works, if you allready know daemontools/runit
the process is very similar, and basically the most important thing to
keep in mind is the directory where the configuration for your services
([run.yml](/post/run.yml)) will be located.

[immortaldir](/post/immortaldir) should be started on boot time and its main
function is to scan the contents of the passed directory searching for files
ending in `.yml` so that it can invoke immortal based on the defined settings.

In case you are lucky and are using [FreeBSD](https://www.freebsd.org/) this can
be very easily done by just adding this lines to `/etc/rc.conf`:

    immortaldir_enable="YES"

By default will use the directory: `/usr/local/etc/immortal`

If you would like to use a different directory, for example `/services` you could add this to `/etc/rc.conf`:

    immortaldir_path='/services'


If you are using Linux, the principle is the same but, you need to define
a directory and then create or configure the system to run on boot time
`immortaldir`, check the [how to install & setup](/post/how-to-install)
and [systemd](/post/systemd/)

That's pretty much what it is needed, once immortaldir is up and running for
starting a service is just a matter or copy or creating a file `service.yml`
within the directory passed to immortaldir and to stop the service just remove
the file or rename it to `service.yml.off`

## Example running same service multiple times

Let's say that you created a client/worker that needs to be polling or listening
to events, this could be a rabbitmq/nats client, for example, and you would like
to run 3 or this "workers" at the same time besides capturing logs per each one.

First, you need to create the configuration file, this could be something like:

```yml
cmd: /path/to/worker -c /path/to/config
env:
    ENVIRONMENT: production
log:
    file: /var/log/wrk-1.log
    age: 86400 # seconds
    num: 7     # int
    size: 1    # MegaBytes
wait: 3 # wait 3 seconds before starting
```

Save the file in `/etc/immortal/wrk1.yml` here we are assuming that
`/etc/immortal` is the directory you previously configured to be used with
[immortaldir](/post/immortal).

After you copy or create the file, the command (/path/to/worker) will
automatically be up and running, to create 2 more workers you need to create 2
more files, just remember to change the log location so that you could have logs
per worker, for example content for `/etc/immortal/wrk2.yml` would be:

```yml
cmd: /path/to/worker -c /path/to/config
env:
    ENVIRONMENT: production
log:
    file: /var/log/wrk-2.log
    age: 86400 # seconds
    num: 7     # int
    size: 1    # MegaBytes
```

> notice that wrk2 has no `wait` option, that means it will start immediately

In this example the name of the files `wrk1.yml` and `wrk2.yml` are
the names that will be used to identify the process, so if you run
[immortalctl](/post/immortalctl) it will print the status for `wrk1` and `wrk2`,
is up to you the names that would like to use.

If you want to permanently stop a worker, you just need to remove the `*.yml`
extension, for me is easy to just add the `.off` suffix, for example:
`wrk2.yml.off`

If need to temporally stop but on boot time start all of then
[immortalctl](/post/immortalctl) becomes very handy, for example to stop only `wrk2`:

    immortalctl stop wrk2

And that's it, by typing again `immortalctl` it will show process that are up
and down with some extra details like the uptime, downtime and the command they
suppose to be running.
