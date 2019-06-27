# Ansible playbooks to build and install Python 3.n on raspian

This is why you should use Arch on your pi.

Running playbook directly on target needs ansible locally installed:

```sh
$ sudo apt install ansible
```

If ansible is already available on the 'master' box just run it from there and
point to the target using -i as usual.

```sh
$ ansible-playbook --ask-become-pass -i localhost, python3.7.yml
```

