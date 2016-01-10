bosun_agent
=========

Installs Bosun's agent _scollector_ either from self deployed packages
or via GitHub binary download.

Requirements
------------

None.

Role Variables
--------------

`_bosun_agent` is a dictionary containg all configuration options. See example for details.

Dependencies
------------

None.

Example Playbook
----------------

```
    - hosts: servers
      vars:
        HOST:
          platform: ARMv5
          subdomain: mysubdomain
          init: upstart
        BOSUN_AGENT:
          install: download
          download_url: "https://github.com/bosun-monitor/bosun/releases/download/0.4.0/scollector-linux-{{ HOST.platform|lower }}"
          upgrade: No
          init: "{{ HOST.init }}"
          server:
            protocol: https
            host: bosun.server.com
            port: 8070
            user: username
            password: secert
          hostname: "{{ ansible_hostname }}.{{ HOST.subdomain }}"
          domain: "lukas_pustina"
          hostgroup: hostgroup_for_bosun
          common:
            tags:
              domain: "mydomain.com"
              hosttype: baremetal
          external_collectors:
            dir: /usr/share/scollector/external_collectors
            template: |+
              #!/bin/bash
              function nowAsEpoch() {
                date +'%s'
              }
              function printValue() {
                local metric=$1
                shift
                local value=$1
                shift
                local timestamp=$(nowAsEpoch)

                echo $metric $timestamp $value $@
              }
            collectors:
              - { template: number_of_process.sh.j2, metric: os.process.count, interval: 60, filename: number_of_process.sh, meta: { ratetype: gauge, unit: "", desc: "The number of currently running processes" } }
          extra_lines: |+
      roles:
        - { role: bosun_agent, tags: [ 'bosun_agent' ], _bosun_agent: "{{ BOSUN_AGENT }}" }
```

License
-------

See LICENSE file.

Author Information
------------------

Initially created by Lukas Pustina [@drivebytesting](https://twitter.com/drivebytesting).

