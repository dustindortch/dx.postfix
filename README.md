# dx.postfix

Ansible role for Postfix

Idempotent implementation of Postfix role allows only defining of desired properties.

All Postfix property names are supported (using a prefix of `dx_postfix_`) with the exception of `2bounce_notice_recipient` which should be written as `dx_postfix_two_bounce_notice_recipient`, instead.

Boolean values of "yes" or "no" should be wrapped in quotes within YAML code to prevent Ansible from converting to "True" and "False" values which would break the `main.cf`.

Conditionals based on the value of `dx_postfix_compatibility_level` are implemented as Jinja conditionals instead of postconf conditionals, but still as ternaries:

## Postconf conditional

```text
${{$compatibility_level} < {1} ? {yes} : {no}}
```

## Jinja conditional

```jinja
{{ (dx_postfix_compatibility_level < 1) | ternary('yes','no') }}
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
       dx_postfix_inet_protocols: ipv4
       dx_postfix_relayhost: "[mail.example.com]"
       dx_postfix_mydomain: "example.com"
       dx_postfix_mynetworks: "192.0.2.0/24"

     tasks:
       - name: telnet package install
         package:
           name: telnet
           state: present

      roles:
        - dx.postfix
