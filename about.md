+++
date = "2016-07-20T17:34:22+02:00"
title = "⭕  immortal"

+++

# What is immortal ?

A *nix cross-platform (OS agnostic) supervisor based on
[daemontools](https://cr.yp.to/daemontools.html) & [runit](http://smarden.org/runit/).

> Improve the wheel, don’t reinvent it


# Why another supervisor ?

Daemontools & runit work very well but don't cover all use cases, mainly when
dealing with applications that follow the zero-downtime deployment pattern
([unicorn](https://unicorn.bogomips.org/)/[gunicorn](http://gunicorn.org/)),
applications that daemonize itself like [nginx](http://nginx.org) or when
distributed logging is required.

Example of a zero-downtime flow using unicorn and signal handling:
[![asciicast](https://asciinema.org/a/80357.png)](https://asciinema.org/a/80357)

⭕  immortal, follows and implements the same logic/principles of daemontools, it
only extends some features to cover more needs, like the:

    -f follow pid

Because of this, it aims to be compatible with existing daemontools or runit
[run](https://cr.yp.to/daemontools/supervise.html) scripts.

Example using the ``-f follow pid`` option supervising unicorn:

[![asciicast](https://asciinema.org/a/108148.png)](https://asciinema.org/a/108148)

When working in distributed "cloud" environments or while doing Continuous
Deployments, exists the need to automate the behavior of the supervisor on
demand, because of this **immortal** can be used with a custom configuration
file ``run.yml`` helping to simplify the deployment flow besides
offering more flexibility.

Example ``run.yml``:

```yaml
cmd: bundle exec unicorn -c unicorn.rb
cwd: /arena/app-1/tmp
env:
    DEBUG: 1
    ENVIRONMENT: production
pid:
    follow: /arena/app-1/unicorn.pid
    parent: /arena/app-1/parent.pid
    child: /arena/app-1/child.pid
log:
    file: /var/log/app-1.log
    age: 86400 # log 1 day
    num: 7     # keep 7 files
    size: 1    # size of file 1MB
logger: filebeat -c filebeat.yml -v -once
user: www
wait: 2 # wait 2 seconds before start
require:
  - foo
  - bar
```

# Manage your process using a JSON API

⭕ **immortal** exposes full control over the process via an Unix socket,
returning status in JSON format allowing with this to query and full manage the
process via http, based on the requirements the services could be exposed via
any web server like Nginx.

Example using curl to query the status of a process:

    curl --unix-socket immortal.sock http:/status -s | jq

Outputs:

```json
{
  "pid": 6940,
  "up": "7.9s",
  "cmd": "./stdout",
  "fpid": false,
  "count": 4
}
```
