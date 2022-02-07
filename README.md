Ansible Role: Restic
====================

[![Build Status](https://app.travis-ci.com/hadret/ansible-role-restic.svg?branch=master)](https://app.travis-ci.com/hadret/ansible-role-restic)

An Ansible role that installs [restic](https://restic.net) on Linux & FreeBSD.

Requirements
------------

None.

Role Variables
--------------

Available variables are listed below, along with default values (see
[defaults/main.yml](defaults/main.yml)):

```
restic_version: '0.12.1'
restic_install_path: '/usr/local/bin'
restic_password_file_path: '/root'
restic_discard_cron_stdout: false

restic_user: root
restic_group: "{{ restic_user }}"
```

* `restic_version` specifies version of the restic binary that should be
installed. It's matched against GitHub repository where the releases are
announced for downloading.
* `restic_install_path` provides path where the restic
binary should be available from (`/usr/local/bin` is exported by
default as part of `$PATH`). It's also the same path that is used by the
wrapper script that is doing the actual backup execution where the name is
constructed as `$restic_install_path/restic-$job_name` (more on that later).
* `restic_password_file_path` provides path to the folder in which password
files for restic repositories should be stored. Each file is constructed as
`$restic_password_file_path/.restic-@job_name`. Those files are hidden (hence
`.` in front of the file) by default and are restricted to the `restic_ user`
and `restic_group` for read-only access. These, by default, are `root`, but it
can be overriden and any valid/already present user can be used. In such case
proper capabilities are going to be ensured for this user.

```
restic_discard_cron_stdout: false
```

Defines whether the cronjob output should be directed to `/dev/null` or not
(it will send e-mail on each execution if this is set to `false`).

```
restic_prometheus_exporter: true
restic_prometheus_output_path: /var/lib/node_exporter/textfile_collector
```

It enables very simple Prometheus integration that is going to write three
values to the default textfile collector location. Please note that enabling
support for this is going to install/require two additional dependencies:
`jq` and `moreutils` (for `sponge`).

```
restic_repos: []
```

This defines an array of restic jobs. Each defined job will create a separate
wrapper script and a password file. Examples can be found in
[defaults/main.yml](defaults/main.yml).

Dependencies
------------

None.

Example Playbook
----------------

```
- hosts: all
  roles:
     - hadret.restic
```

Credits
-------

This role builds on the shoulders of
[donat-b/ansible-restic](https://github.com/donat-b/ansible-restic) and
[paulfantom/ansible-restic](https://github.com/paulfantom/ansible-restic). All
of the `molecule` tests and CI configuration are based on work of
[geerlingguy](https://github.com/geerlingguy). A lot of parts for the Prometheus integration had been taken
directly from [this Restic forum thread](https://forum.restic.net/t/restic-grafana-dashboard/1662/6).

License
-------

MIT

Author Information
------------------

This role was somewhat assembled in 2018 by [Filip Chabik](https://chabik.com).
