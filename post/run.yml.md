+++
date = "2016-07-06T20:35:39+02:00"
description = "config file"
title = "run.yml"

+++

## immortal -c run.yml

A configuration file for running/logging/supervising:

```yaml
cmd: bundle exec unicorn -c unicorn.rb
cwd: /arena/app-1
env:
    DEBUG: 1
    ENVIRONMENT: production
pid:
    follow: /arena/app-1/unicorn.pid
    parent: /arena/app-1/parent.pid
    child: /arena/app-1/child.pid
log:
    file: /var/log/app-1.log
    age: 86400 # seconds
    num: 7     # int
    size: 1    # MegaBytes
    timestamp: true # will add timesamp to log
logger: logger -t unicorn
user: www
wait: 1
require:
  - foo
  - bar
```

One of the main ideas behind the ``run.yml`` is to eliminate an
operations/devops dependency while deploying.

The application "micro service" can be shipped with a ``run.yml`` and when
doing a CI & CD flow, by simple updating the ``run.yml`` file, the **immortal**
supervisor could stop, restart, pause, resume the service without need to
logging the server and execute does steps.

See the [ansible playbook example](/post/ansible)

# require

The `require` option helps to verify that other services are running before
starting, for example, having this services:

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

### example using su

In some cases the full environment of a user is required to be loaded before
running the command, therefore the use of the command `su user -c` becomes
handy, example:

```yaml
cmd: su ec2_user -c "/path/to/bin"
env:
    ENVIRONMENT: production
    AWS_ACCESS_KEY_ID: access
    AWS_SECRET_ACCESS_KEY: secret
    AWS_ACCESS_KEY: access
    AWS_SECRET_KEY: secret
log:
    file: /var/log/bin.log
    age: 86400 # seconds
    num: 7     # int
    size: 1    # MegaBytes
```

# timestamp

By default the option `timestamp` within the `log` is set to
false, this is in favor of applications that follow the
[12 factor](https://12factor.net/logs) and in cases where log
is structured "[JSON](https://en.wikipedia.org/wiki/JSON)", it
could be parsed easily.

In case a time stamp is required simple set `timestamp`, example:

```yaml
log:
    file: /var/log/bin.log
    age: 86400 # seconds
    num: 7     # int
    size: 1    # MegaBytes
    timestamp: true
```

This will prefix each line with a `RFC3339Nano` time stamp:

```log
2017-08-03T12:10:31.040165255Z your log ...
```
