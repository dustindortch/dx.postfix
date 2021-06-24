# dx.postfix

Ansible role for Postfix

Idempotent implementation of Postfix role allows only defining of desired properties.

All Postfix property names are supported with the exception of `2bounce_notice_recipient` which should be written as `two_bounce_notice_recipient`, instead.

Boolean values of "yes" or "no" should be wrapped in quotes within YAML code to prevent Ansible from converting to "True" and "False" values which would break the `main.cf`.

Conditionals based on the value of `compatibility_level` are implemented as Jinja conditionals instead of postconf conditionals, but still as ternaries:

## Postconf conditional

```text
${{$compatibility_level} < {1} ? {yes} : {no}}
```

## Jinja conditional

```jinja
{{ (compatibility_level < 1) | ternary('yes','no') }}
```

## Example - SMTP Relay

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
