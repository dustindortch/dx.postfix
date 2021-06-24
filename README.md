# dx.postfix
Ansible role for Postfix

Idempotent implementation of Postfix role allows only defining of desired properties.

Example - SMTP Relay:
```yaml
---
  - name: postfix configura as mail relay
    become: true
    gather_facts: true
    hosts: all

    pre_tasks:
      - name: yum update
        yum:
          name: '*'
          state: latest

     vars:
       inet_protocols: ipv4
       relayhost: "[mail.example.com]"
       mydomain: "example.com"
       mynetworks: "192.0.2.0/24"

     tasks:
       - name: telnet package install
         package:
           name: telnet
           state: present

      roles:
        - dx.postfix
