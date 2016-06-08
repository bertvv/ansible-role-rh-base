# Ansible role `rh-base`

Ansible role for basic setup of a server with a RedHat-based Linux distribution (CentOS, Fedora, RHEL, ...). Specifically, the responsibilities of this role are to:

- Manage repositories,
- Manage package installation and removal,
- Turn specified services on or off,
- Create users and groups,
- Set up an administrator account with an SSH key,
- Apply basic security settings, like turning on SELinux and the firewall.

## Requirements

No specific requirements

## Role Variables


| Variable                          | Default   | Comments (type)                                                                                                       |
| :---                              | :---      | :---                                                                                                                  |
| `rhbase_admin_ssh_key`            | -         | The public SSH key for the admin user that allows her to log in without a password. The user should exist.            |
| `rhbase_admin_user`               | -         | The name of the user that will manage this machine.                                                                   |
| `rhbase_override_firewalld_zones` | false     | When set, allows NetworkManager to override firewall zones set by the administrator†.                                 |
| `rhbase_enable_repos`             | []        | List of dicts specifying repositories to be enabled. See below for details.                                           |
| `rhbase_exclude_from_update`      | []        | List of packages to be excluded from an update. Wildcards allowed, e.g. `kernel*`.                                    |
| `rhbase_firewall_allow_ports`     | []        | List of ports to be allowed to pass through the firewall, e.g. 80/tcp, 53/udp, etc.                                   |
| `rhbase_firewall_allow_services`  | []        | List of services to be allowed to pass through the firewall, e.g. http, dns, etc..‡                                   |
| `rhbase_hosts_entry`              | true      | When set, an entry is added to `/etc/hosts` with the machine's host name. This speeds up gathering facts.             |
| `rhbase_install_packages`         | []        | List of packages that should be installed. URLs are also allowed.                                                     |
| `rhbase_motd`                     | false     | When set, a custom `/etc/motd` is installed with info about the host name and IP addresses.                           |
| `rhbase_remove_packages`          | []        | List of packages that should **not** be installed                                                                     |
| `rhbase_repo_exclude`             | []        | List of repositories that should be disabled in yum/dnf.conf                                                          |
| `rhbase_repo_gpgcheck`            | false     | When set, GPG checks will be performed when installing packages.                                                      |
| `rhbase_repo_installonly_limit`   | 3         | The maximum number of versions of a package (e.g. kernel) that can be installed simultaneously. Should be at least 2. |
| `rhbase_repo_remove_dependencies` | true      | When set, dependencies that become unused after removing a package will be removed as well.                           |
| `rhbase_repositories`             | []        | List of RPM packages (including URLs) that install external repositories (e.g. `epel-release`).                       |
| `rhbase_selinux_state`            | enforcing | The default SELinux state for the system. Just [leave this as is](http://stopdisablingselinux.com/).                  |
| `rhbase_start_services`           | []        | List of services that should be running and enabled.                                                                  |
| `rhbase_stop_services`            | []        | List of services that should **not** be running                                                                       |
| `rhbase_update`                   | false     | When set, a package update will be performed.                                                                         |
| `rhbase_user_groups`              | []        | List of user groups that should be present.                                                                           |
| `rhbase_users`                    | []        | List of dicts specifying users that should be present. See below for an example.                                      |

† This is a workaround for [CentOS bug #7407](https://bugs.centos.org/view.php?id=7407). NetworkManager by default manages firewall zones, which overrides rules you add with --permanent.
‡ A complete list of valid values for `rhbase_firewall_allow_services` can be enumerated with the command `firewall-cmd --get-services`.

### Enabling repositories

Enable repositories by specifying `el7_enable_repositories` as a list of dicts with keys `name:` (required) and `section:` (optional), e.g.:

```Yaml
el7_enable_repositories:
  - name: CentOS-fasttrack
    section: fasttrack
  - name: epel-testing
```

When the section is not specified, it defaults to the repository name.

### Adding users

Users are specified by dicts like this:

```Yaml
el7_users:
  - name: johndoe
    comment: 'John Doe'
    groups:
      - users
      - devs
    password: '$6$WIFkXf07Kn3kALDp$fHbqRKztuufS895easdT [...]'
```

Optionally, you can specify the `shell`, which defaults to `/bin/bash`.

## Dependencies

No dependencies.

## Example Playbook

See the [test playbook](https://github.com/bertvv/ansible-role-rhbase/blob/master/tests/test.yml)

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

