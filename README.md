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

## Playbook

Be sure to use the playbook in the repo, below is just advertising.

```ansible

# An ansible role to configure python3.7 on a Raspberry Pi.
# Ensure ansible is here: $ sudo apt install ansible
# Use the playbook: $ ansible-playbook -i localhost python3.7.yml
#
---
- hosts: all
  vars:
    version: 3.7.3
    threads: 4
  tasks:
    - name: "Python-{{version}} run&build dependencies"
      become: yes
      apt: name={{item}}
      with_items:
        - build-essential
        - tk-dev
        - libncurses5-dev
        - libncursesw5-dev
        - libreadline6-dev
        - libdb5.3-dev
        - libgdbm-dev
        - libsqlite3-dev
        - libssl-dev
        - libbz2-dev
        - libexpat1-dev
        - liblzma-dev
        - zlib1g-dev
        - libffi-dev
    - name: "Download Python-{{version}}"
      get_url:
        url="https://www.python.org/ftp/python/{{version}}/Python-{{version}}.tar.xz"
        dest="/tmp/Python-{{version}}.tar.xz"
    - name: "Unarchive Python-{{version}}"
      unarchive:
        src="/tmp/Python-{{version}}.tar.xz"
        dest="/tmp/"
        copy=no
        creates="/tmp/Python-{{version}}"
    - name: "Configure Python-{{version}} build"
      command: ./configure --enable-optimizations
      args:
        chdir: "/tmp/Python-{{version}}"
        creates: "/tmp/Python-{{version}}/Makefile"
    - name: "Build Python-{{version}}"
      # not using make module to be able to use -j and creates option to fully skip step
      command: make -j{{threads}}
      args:
        chdir: "/tmp/Python-{{version}}"
        creates: "/tmp/Python-{{version}}/python"
    - name: "Install Python-{{version}}"
      become: yes
      make:
        chdir: "/tmp/Python-{{version}}"
        target: altinstall

```
