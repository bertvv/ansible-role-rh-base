# Change log

This file contains al notable changes to the bertvv.rh-base Ansible role. This file adheres to the guidelines of [http://keepachangelog.com/](http://keepachangelog.com/). Versioning follows [Semantic Versioning](http://semver.org/).

## 2.2.0 - 2017-09-28

### Added

- (GH-5) Variable `rhbase_tz` sets the TZ environment variable to save system calls

### Changed

- Give keys of the `rhbase_users` variable default values. Only user name is required. See the <README.md> for details.
- Upgrade base boxes to latest versions: CentOS 7.4 and Fedora 26
- (GH-6) Ensure /var/log/journal/ exists so the system can keep persistent logs

## 2.1.0 - 2016-11-24

### Added

- Variable `rhbase_firewall_interfaces` can now be used to add a network interface to the public zone.

### Changed

- The role now ensures essential systemd services (e.g. journald, tmpfiles) are running. The difference with previous version is that two services were added and they are now enumerated in a variable.

## 2.0.0 - 2016-10-30

### Added

- Added ‘exclude=’ option to `/etc/dnf.conf` and `/etc/yum.conf` and the variable `rhbase_repo_exclude_from_updates`
- Added task that creates `/etc/machine-id` if necessary. When this file is not set up correctly (with `systemd-machine-id-setup`), `systemd-journald` cannot run.

### Changed

- (RH-2) Renamed variables `rhbase_admin_user` to `rhbase_ssh_user` and `rhbase_admin_ssh_key` to `rhbase_ssh_key`. The previous names were confusing, as the role did nothing special to make this user an admin (i.e. member of `wheel`). This is a breaking change, hence the major version bump.
- Added `firewalld` to the list of packages to be installed as dependencies

## 1.0.2 - 2016-09-20

Bugfix release

### Changed

- Fixed tag names in tasks

## 1.0.1 - 2016-09-21

Bugfix release

### Changed

- Added handler to restart the firewall daemon
- Added forgotten role tag to a task

## 1.0.0 - 2016-06-08

First release!

### Added

- Functionality from roles [bertvv.el7](https://galaxy.ansible.com/bertvv/el7) and [bertvv.fedora](https://galaxy.ansible.com/bertvv/fedora).

