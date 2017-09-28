# Ansible role `rh-base`

Ansible role for basic setup of a server with a RedHat-based Linux distribution (CentOS, Fedora, RHEL, ...). Specifically, the responsibilities of this role are to:

- Manage repositories,
- Manage package installation and removal,
- Turn specified services on or off,
- Create users and groups,
- Set up an administrator account with an SSH key,
- Apply basic security settings, like turning on SELinux and the firewall,
- Manage firewall rules (in the public zone).

## Requirements

No specific requirements

## Role Variables

| Variable                          | Default         | Comments (type)                                                                                                       |
| :---                              | :---            | :---                                                                                                                  |
| `rhbase_enable_repos`             | []              | List of dicts specifying repositories to be enabled. See below for details.                                           |
| `rhbase_firewall_allow_ports`     | []              | List of ports to be allowed to pass through the firewall, e.g. 80/tcp, 53/udp, etc.                                   |
| `rhbase_firewall_allow_services`  | []              | List of services to be allowed to pass through the firewall, e.g. http, dns, etc.(1)                                  |
| `rhbase_firewall_interfaces`      | []              | List of network interfaces to be added to the public zone of the firewall ruleset.                                    |
| `rhbase_hosts_entry`              | true            | When set, an entry is added to `/etc/hosts` with the machine's host name. This speeds up gathering facts.             |
| `rhbase_install_packages`         | []              | List of packages that should be installed. URLs are also allowed.                                                     |
| `rhbase_motd`                     | false           | When set, a custom `/etc/motd` is installed with info about the host name and IP addresses.                           |
| `rhbase_override_firewalld_zones` | false           | When set, allows NetworkManager to override firewall zones set by the administrator(2).                               |
| `rhbase_remove_packages`          | []              | List of packages that should **not** be installed                                                                     |
| `rhbase_repo_exclude_from_update` | []              | List of packages to be excluded from an update. Wildcards allowed, e.g. `kernel*`.                                    |
| `rhbase_repo_exclude`             | []              | List of repositories that should be disabled in yum/dnf.conf                                                          |
| `rhbase_repo_gpgcheck`            | false           | When set, GPG checks will be performed when installing packages.                                                      |
| `rhbase_repo_installonly_limit`   | 3               | The maximum number of versions of a package (e.g. kernel) that can be installed simultaneously. Should be at least 2. |
| `rhbase_repo_remove_dependencies` | true            | When set, dependencies that become unused after removing a package will be removed as well.                           |
| `rhbase_repositories`             | []              | List of RPM packages (including URLs) that install external repositories (e.g. `epel-release`).                       |
| `rhbase_selinux_state`            | enforcing       | The default SELinux state for the system. Just [leave this as is](http://stopdisablingselinux.com/).                  |
| `rhbase_ssh_key`                  | -               | The public SSH key for the admin user that allows her to log in without a password. The user should exist.            |
| `rhbase_ssh_user`                 | -               | The name of the user that will manage this machine. The SSH key will be installed into the user's home directory.(3)  |
| `rhbase_start_services`           | []              | List of services that should be running and enabled.                                                                  |
| `rhbase_stop_services`            | []              | List of services that should **not** be running                                                                       |
| `rhbase_tz`                       | :/etc/localtime | Sets the `$TZ` environment variable (4)                                                                               |
| `rhbase_update`                   | false           | When set, a package update will be performed.                                                                         |
| `rhbase_user_groups`              | []              | List of user groups that should be present.                                                                           |
| `rhbase_users`                    | []              | List of dicts specifying users that should be present. See below for an example.                                      |

**Remarks:**

(1) A complete list of valid values for `rhbase_firewall_allow_services` can be enumerated with the command `firewall-cmd --get-services`.

(2) This is a workaround for [CentOS bug #7407](https://bugs.centos.org/view.php?id=7407). NetworkManager by default manages firewall zones, which overrides rules you add with `--permanent`.

(3) Setting the variable `rhbase_ssh_user` does not actually create a user, but installs the `rhbase_ssh_key` in that user's home directory (`~/.ssh/authorized_keys`). Consequently, `rhbase_ssh_user` should be the name of an existing user, specified in `rhbase_users`.

(4) setting `$TZ` variable may reduce the number of system calls. See <https://blog.packagecloud.io/eng/2017/02/21/set-environment-variable-save-thousands-of-system-calls/>

### Enabling repositories

Enable (installed, but disabled) repositories by specifying `rhbase_enable_repos` as a list of dicts with keys `name:` (required) and `section:` (optional), e.g.:

```Yaml
rhbase_enable_repos:
  - name: CentOS-fasttrack
    section: fasttrack
  - name: epel-testing
```

When the section is not specified, it defaults to the repository name.

### Adding users

Users are specified by dicts like this:

```Yaml
rhbase_users:
  - name: johndoe
    comment: 'John Doe'
    groups:
      - users
      - devs
    password: '$6$WIFkXf07Kn3kALDp$fHbqRKztuufS895easdT [...]'
  - name: janedoe
```

The only mandatory key is `name`.

| Key        | Required | Default     | Comments                               |
| :---       | :---:    | :---:       | :---                                   |
| `name`     | yes      | -           | The user name                          |
| `comment`  | no       | ''          | Comment string                         |
| `shell`    | no       | '/bin/bash' | The user's command shell               |
| `groups`   | no       | []          | Groups this user should be added to(1) |
| `password` | no       | '!!'        | The user's password hash(2)            |

**Remarks:**

(1) If you want to make a user an administrator, make sure they are member of the group `wheel` (See [RedHat System Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/chap-Gaining_Privileges.html#sect-Gaining_Privileges-The_su_Command).

(2) The password should be specified as a hash, as returned by [crypt(3)](http://man7.org/linux/man-pages/man3/crypt.3.html), in the form `$algo$salt$hash`. For tests and proof-of-concept VMs, you can take a look at <https://www.mkpasswd.net/> for generating hashes in the correct form. When you do, be sure to remove `rounds=5000$` from the resulting hash string.

## Dependencies

No dependencies.

## Example Playbook

See the [test playbook](https://github.com/bertvv/ansible-role-rh-base/blob/tests/test.yml)

## Testing

Tests for this role are provided in the form of a Vagrant environment that is kept in a separate branch, `tests`. I use [git-worktree(1)](https://git-scm.com/docs/git-worktree) to include the test code into the working directory. Instructions for running the tests:

1. Fetch the tests branch: `git fetch origin tests`
2. Create a Git worktree for the test code: `git worktree add tests tests` (remark: this requires at least Git v2.5.0). This will create a directory `tests/`.
3. `cd tests/`
4. `vagrant up` will then create VMs of the supported platforms and apply a test playbook (`test.yml`).

## Contributing

Issues, feature requests, ideas are appreciated and can be posted in the Issues section. Pull requests are also very welcome. Preferably, create a topic branch and when submitting, squash your commits into one (with a descriptive message).

## License

BSD

## Contributors

- Bert Van Vreckem (maintainer)
- [Jeroen De Meerleer](https://github.com/JeroenED)

