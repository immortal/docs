+++
title = "compile from source"
date = "2017-03-20T16:17:00+00:00"
hidden = true

+++

# pre-compiled (Linux)

Using https://packagecloud.io/immortal/immortal

For `rpm`:

    curl -s https://packagecloud.io/install/repositories/immortal/immortal/script.rpm.sh | sudo bash


For `deb`:

    curl -s https://packagecloud.io/install/repositories/immortal/immortal/script.deb.sh | sudo bash



# Compile from source

Setup go environment https://golang.org/doc/install

> go >= 1.9 is required

For example using $HOME/go for your workspace

```bash
$ export GOPATH=$HOME/go
```

Create the directory:

```bash
$ mkdir -p $HOME/go/src/github.com/immortal
```

Clone project into that directory:

```bash
$ git clone git@github.com:immortal/immortal.git $HOME/go/src/github.com/immortal/immortal
```

Build by just typing make:

```
$ cd $HOME/go/src/github.com/immortal/immortal
$ make
```

To install/uninstall:

```
$ make install
$ make uninstall
```

## Help making the port/package

If you are familiar with the platform, would be nice if could create a native
port in order make more simple the installation process.

Currently there are only ports for [FreeBSD](/freebsd) and [macOS](/mac)

# Saltstack

[saltstack](https://saltstack.com/) formula: https://github.com/saltstack-formulas/immortal-formula
