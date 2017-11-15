+++
title = "How it works"
date = "2017-03-21T12:04:36+01:00"
description = ""
tags = ["immortal", "immortalctl", "immortaldir"]

+++

⭕  `immortal` runs a command or script detached from the controlling terminal
as a Unix daemon, it will supervise and restart the service if it has
been terminated. The service can be controlled by querying a Unix socket
"immortal.sock" (`immortalctl`), this allows to remotely have full control over
the service if required by exposing the socket using a web server like Nginx.

```html
immortal    - Starts and monitor a service
immortaldir - Directory to scan for services *.yml
immortalctl - Control existing services
```

In a nutshell, `immortal` is a command that runs forever an application or
service.  In most common cases `immortal` is called by `imortaldir` which
searches for immortal configuration files [run.yml](/post/run.yml) and ideally
starts at boot time in order to persist across reboots.

In some cases there is a need to "daemonize" a command but not to keep it
persistent across reboots, for this cases [immortal](/post/immortal) can be
used directly with out the need of a [configuration file](/post/run.yml)
or either no need to use [immortaldir](/post/immortaldir).

[immortalctl](/post/immortalctl) still will show the status of existing
processes besides been available to control them.


Example:

{{<asciinema  108148>}}
