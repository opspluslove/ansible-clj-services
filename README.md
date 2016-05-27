[![Build Status](https://travis-ci.org/opspluslove/ansible-clj-services.svg?branch=master)](https://travis-ci.org/opspluslove/ansible-clj-services)

clj-services
=========

In your [requirements file](https://galaxy.ansible.com/intro):

```
- src: opspluslove.clj-services
  version: v1.0.1
```

----

Ansible role to deploy and install clojure services.

This role is a bit specific. It builds an uberjar on the local machine (the one running ansible) then push it to the remote and setup a systemd service to run it.

Requirements
------------

There is two set of requirements. First, **on the local machine** you will need a Clojure dev environment so at least the `JDK` and `a build tool` ([leiningen](http://leiningen.org/), [boot-clj](http://boot-clj.com/), something else).
Then **on the remote** you will need the `JRE` and `systemd`.

Role Variables
--------------

```
# Defaults
clj_services: []
systemd_local_services_path: /usr/lib/systemd/system/

# clj_services example
clj_services:
  - name: my-clj-service
    description: Awesome service written in Clojure
    enabled: no
    build_command: boot build
    local_directory: ~/Development/Clojure/my-clj-service
    uberjar_path: target/my-clj-service.jar
    user: service-user
    # Environment variables for the service
    envs:
      port: 3000
      key2: value2
      key3: value3
    # Configure the systemd service
    service:
      restart: "no" # default to "always"
      stdin: /dev/random # default to "null"
      stdout: syslog # default "syslog"
      stderr: syslog # default "syslog"
      syslog_identifier: \"%nweird%npattern\" # default to \"%n\"
      type: oneshot # not specified by default
```

Example Playbook
----------------

```
- hosts: main-server
  remote_user: root
  tags:
    - my-clj-bot

  vars:
    clj_services:
      - name: my-clj-bot
        description: Bot written in Clojure
        enabled: yes
        build_command: boot build
        local_directory: ~/Development/Clojure/awesome-bot
        uberjar_path: target/bot.jar
        user: bots
        envs:
          port: 3000

  roles:
    - opspluslove.clojure
    - opspluslove.clj-services
```
