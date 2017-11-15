+++
date = "2010-03-26T23:10:08+02:00"
description = "playbook example"
title = "Ansible"

+++

Example of a basic [ansible](https://www.ansible.com/) playbook for deploying a
micro service using a [configuration file](/post/run.yml):

```yaml
- hosts: your-host
  become: true
  tasks:
  - name: create arena
    file: path=/arena/foo/releases state=directory

  - name: set now variable
    set_fact:
      now: "{{ ansible_date_time.iso8601 }}"

  - name: create arena release dir
    file: path=/arena/foo/releases/{{ now }} state=directory

  - name: download app.bz2
    get_url:
      url: "http://domain.tld/foo/{{ 'freebsd' if ansible_os_family == 'FreeBSD' else 'linux' }}/app.tar.bz2"
      dest: /arena/foo/releases/{{ now }}

  - name: unarchive app.bz2
    command: tar -jxf /arena/foo/releases/{{ now }}/app.tar.bz2
    args:
      chdir: /arena/foo/releases/{{ now }}

  - name: link to current
    file:
      src: /arena/foo/releases/{{ now }}
      dest: /arena/foo/current
      state: link

  - name: update foo.yml
    copy:
      dest: "{{ '/usr/local/etc/immortal' if ansible_os_family == 'FreeBSD' else '/etc/immortal' }}/foo.yml"
      content: |
        # {{ now }}
        cmd: /arena/foo/current/app
        cwd: /arena/foo/
        log:
            file: /var/log/foo.log
            age: 86400 # seconds
            num: 7     # int
            size: 1    # MegaBytes
        user: www
```

Notice the last task `update foo.yml`:

```
- name: update foo.yml
  copy:
    dest: /usr/local/etc/foo.yml
    content: |
      # {{ now }}
      cmd: /arena/foo/current/app
      cwd: /arena/foo/
      log:
          file: /var/log/foo.log
          age: 86400 # seconds
          num: 7     # int
          size: 1    # MegaBytes
      user: www
```

Notice the first line in the content:

```html
# {{ now }}
```

That will modify/update the configuration file, therefore it is enough to
trigger a restart, the updated file `/usr/local/etc/foo.yml`, will look
something like:

```
# 2017-03-29T16:36:39Z
cmd: /arena/foo/current/app
cwd: /arena/foo/
log:
    file: /var/log/foo.log
    age: 86400 # seconds
    num: 7     # int
    size: 1    # MegaBytes
user: www
```

The deployment schema in this example is as follow:

```html
/arena
|--service-name-1
|  |--releases
|  |  `--ISO8601-time
|  `--@current
`--service-name-2
   |--releases
   |  `--ISO8601-time
   `--@current
```
