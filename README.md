# Ansible role `rh-base`

An Ansible role for PURPOSE. Specifically, the responsibilities of this role are to:

-

## Requirements

No specific requirements

## Role Variables


| Variable   | Required | Default | Comments (type)  |
| :---       | :---     | :---    | :---             |
| `role_var` | no       | -       | (scalar) PURPOSE |

## Dependencies

No dependencies.

## Example Playbook

See the [test playbook](tests/test.yml)

## Testing

Tests for this role are provided in the form of a Vagrant environment that is kept in a separate branch, `tests`. I use [git-worktree(1)](https://git-scm.com/docs/git-worktree) to include the test code into the working directory. Instructions for running the tests:

1. Fetch the tests branch: `git fetch origin tests`
2. Create a Git worktree for the test code: `git worktree add tests tests` (remark: this requires at least Git v2.5.0). This will create a directory `tests/`.
3. `cd tests/`
4. `vagrant up` will then create a VM and apply a test playbook (`test.yml`).

You may want to change the base box into one that you like. The current one, [bertvv/centos72](https://atlas.hashicorp.com/bertvv/boxes/centos72) was generated using a Packer template from the [Boxcutter project](https://github.com/boxcutter/centos) with a few modifications.

## Contributing

Issues, feature requests, ideas are appreciated and can be posted in the Issues section. Pull requests are also very welcome. Preferably, create a topic branch and when submitting, squash your commits into one (with a descriptive message).

## License

BSD

## Author Information

Bert Van Vreckem (bert.vanvreckem@gmail.com)

