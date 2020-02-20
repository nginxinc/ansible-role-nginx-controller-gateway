NGINX Controller Gateway
========================

Upsert (create and update) gateways in NGINX Controller to support ingress for Applications and Components.

Requirements
------------

Role Variables
--------------

### Required Variables

`controller_fqdn` - FQDN of the controller instance
`environment_name` - Environment the gateway is associated with
`gateway.name` - Name of the gateway
`ingress.uris` - URI for the gateway to service
`ingress.placement` - Instance(s) this gateway configuration is applied to (these must exist first)

### Template Variables

This role has multiple template related variables. The descriptions and defaults for all these variables can be found in **[vars/main.yml](./vars/main.yml)**

Dependencies
------------

Example Playbook
----------------

```yaml
- hosts: localhost
  gather_facts: no

  tasks:
    - name: Retrieve the NGINX Controller auth token
      include_role:
        name: nginx_controller_generate_token
      vars:
        user_email: "user@example.com"
        user_password: "mySecurePassword"
        controller_fqdn: "controller.mydomain.com"

    - name: Create a gateway
      include_role:
        name: nginx_controller_gateway
      vars:
        controller_fqdn: "controller.example.local"
        environment_name: "production-us-west"
        # controller_auth_token: output by previous role in example
        gateway:
          name: lending
          displayName: "Shared Public Lending BU Gateway"
          description: "Routes all non special Lending applications"
          tags:
            - production
            - us-west
            - lending
        ingress:
          uris:
            - "http://mortgage.acmefinancial.net"
            - "https://mortgage.acmefinancial.net"
            - "http://ratecalculator.acmefinancial.net"
            - "https://ratecalculator.acmefinancial.net"
          tls:
            certRef:
              ref: "/services/environments/lending-prod/certs/star.acmefinancial.net"
            protocols:
              - "TLSv1.3"
              - "TLSv1.2"
          placement:
            instanceRefs:
              - "/infrastructure/locations/unspecified/instances/2"
              - "/infrastructure/locations/unspecified/instances/4"
```

License
-------

[Apache License, Version 2.0](./LICENSE)

Author Information
------------------

brianehlert
