# Phansible Role: RabbitMQ

[![CI](https://github.com/phansible/role-rabbitmq/actions/workflows/ci.yml/badge.svg)](https://github.com/phansible/role-rabbitmq/actions/workflows/ci.yml)

An Ansible Role that installs [RabbitMQ](https://www.rabbitmq.com/) and a compatible Erlang from the Team RabbitMQ apt repositories, specifically created with [Phansible](http://phansible.com/) in mind.

## Requirements

- Ubuntu 22.04, 24.04, 26.04 or Debian 12, 13 (x86_64; Team RabbitMQ publishes Erlang for amd64 only)
- Ansible >= 2.15 with the `community.rabbitmq` collection (shipped with the `ansible` package)

## Role Variables

All variables live under one `rabbitmq` dictionary, so override it as a whole:

```yaml
rabbitmq:
    hostname: "{{ ansible_facts.hostname }}"
    user: "user"
    password: "password"
    plugins: []
```

- `hostname` (default `{{ ansible_facts.hostname }}`): host part of the node name (`rabbit@<hostname>`) used when managing the user.
- `user` (default `user`): user created with the `administrator` tag and full permissions on the `/` vhost.
- `password` (default `password`): password for that user, set only when the user is created.
- `plugins` (default `[]`): plugins to enable, for example `rabbitmq_management`.

`rabbitmq_series` (default `"4.4"`) is a separate top-level variable: the RabbitMQ release series to install. An apt preferences file pins `rabbitmq-server` to `<series>.*`, so patch releases within the series install and upgrade as usual, but a new series never arrives through a plain `apt upgrade`. RabbitMQ only supports upgrading one series at a time, after the previous series' feature flags are enabled, so a series upgrade should be a deliberate step: enable all feature flags (`rabbitmqctl enable_feature_flag all`), bump this variable by one series, then upgrade the package. Erlang 27 from the Team RabbitMQ repositories works with both supported series (4.3 and 4.4). The pin priority is below 1000, so lowering the variable never downgrades an installed broker.

## Dependencies

None.

## Example Playbook

```yaml
- hosts: all
  become: yes
  vars:
    rabbitmq:
      hostname: "{{ ansible_facts.hostname }}"
      user: app
      password: secret
      plugins: [rabbitmq_management]
    rabbitmq_series: "4.4"
  roles:
    - role-rabbitmq
```

## License

This role is released under the terms of the license specified in the repository or if not specified, under the [MIT license](https://raw.githubusercontent.com/phansible/role-rabbitmq/master/LICENSE).

## Author Information

This role was created in 2015 by [The Phansible Team & Contributors](https://github.com/phansible/role-rabbitmq/graphs/contributors), credits go also to [Jeff Geerling](http://jeffgeerling.com/), author of [Ansible for DevOps](http://ansiblefordevops.com/) and source of inspiration.
