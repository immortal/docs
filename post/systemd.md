---
title: "Systemd"
date:  "2010-03-21T23:10:07+02:00"
description: "immortaldir.service"
---

To start [immortaldir](/post/immortaldir/) on Linux with
[systemd](https://en.wikipedia.org/wiki/Systemd), put this script in
`/etc/systemd/system/immortaldir.service`:

```sh
[Unit]
Description=immortaldir
After=network.target

[Service]
ExecStart=/usr/bin/immortaldir /etc/immortal 2>&1 | logger -t immortaldir
KillMode=process
Restart=always
Restart=on-failure
Type=simple
User=root

[Install]
WantedBy=multi-user.target
```
