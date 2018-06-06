+++
date = "2010-03-22T23:34:54+02:00"
description = "dpkg -i immortal_amd64.deb"
title = "How to install & setup"
+++

At the moment of writing this document currently **immortal** can be installed
in [macOS via homebrew](/mac/) and [FreeBSD (ports/pkg)](/freebsd/), therefore
if you are familiar with your current operating system would be nice if you
could [contribute](https://github.com/immortal/immortal/blob/master/CONTRIBUTING.md#getting-started)
by creating a native port in order to make more simple the installation process.

## Installing on Linux

The easy way so far is to install a pre-compiled release, [download the latest one](https://packagecloud.io/immortal/immortal) that matches your architecture.

[{{< figure src="https://img.shields.io/badge/deb-packagecloud.io-844fec.svg" class="badge" >}}](https://packagecloud.io/immortal/immortal)

If need `deb` you could use:

    curl -s https://packagecloud.io/install/repositories/immortal/immortal/script.deb.sh | sudo bash

[{{< figure src="https://img.shields.io/badge/rpm-packagecloud.io-844fec.svg" class="badge" >}}](https://packagecloud.io/immortal/immortal)

If need  `rpm` you could use:

    curl -s https://packagecloud.io/install/repositories/immortal/immortal/script.rpm.sh | sudo bash

Or get the file directly, for example an (*.deb):

    wget --content-disposition https://packagecloud.io/immortal/immortal/packages/debian/stretch/immortal_0.19.0_amd64.deb/download.deb

Then install it using:

```sh
sudo dpkg -i immortal_0.19.0_amd64.deb
```

In this case it will install version `0.19.0` for amd64, you may still need to configure [immortaldir](/post/immortaldir) to start on boot time.

## Setup immortaldir

To make your applications persistent across reboots,
[immortaldir](/post/immortaldir/) needs to start on boot time so that it
launches your applications every time your system is booted.

The main requirement is to create a directory that will contain
all your [*.yml](/post/run.yml) files, it can be `/etc/immortal`,
`/usr/local/etc/immortal` or either `/var/services` at the end this directory
needs to be passed as the first argument to [immortaldir](/post/immortaldir)

For Linux systems like ubuntu 14 with
[start-stop-daemon](https://manpages.debian.org/jessie/dpkg/start-stop-daemon.8.en.html),
you could put this script in `/etc/init.d/immortaldir`:

```sh
#!/bin/sh

# immortaldir start-stop-daemon
set -e

NAME=immortaldir
PIDFILE=/var/run/$NAME.pid
DAEMON=/usr/bin/immortaldir
DAEMON_OPTS="/etc/immortal"

export PATH="${PATH:+$PATH:}/usr/sbin:/sbin"

case "$1" in
  start)
        echo -n "Starting daemon: "$NAME
	start-stop-daemon --start \
    --quiet \
    --oknodo \
    --make-pidfile \
    --pidfile $PIDFILE \
    --background \
    --startas /bin/sh \
    -- -c "exec $DAEMON $DAEMON_OPTS 2>&1 | logger -t immortaldir"
    echo "."
	;;
  stop)
        echo -n "Stopping daemon: "$NAME
        pkill $NAME
        echo "."
	;;
  *)
	echo "Usage: "$1" {start|stop}"
	exit 1
esac

exit 0
```

On Linux with systemd, put [this script](/post/systemd/) in `/etc/systemd/system/immortaldir.service`
