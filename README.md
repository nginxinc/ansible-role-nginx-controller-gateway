NGINX Controller Gateway
========================

Upsert (create and update) gateways in NGINX Controller to support ingress for Applications and Components.

Requirements
------------

[NGINX Controller](https://www.nginx.com/products/nginx-controller/)

Role Variables
--------------

### Required Variables

`controller.fqdn` - FQDN of the NGINX Controller instance

`controller.auth_token` - Authentication token for NGINX Controller

`environmentName` - Environment the gateway is associated with

`gateway.metadata.name` - Name of the gateway

`gateway.ingress.uris` - URI for the gateway to service

`gateway.ingress.placement.instanceRefs` - Instance(s) this gateway configuration is applied to (these must exist first)

### Template Variables

This role has multiple template related variables. The descriptions and defaults for all these variables can be found in **[vars/main.yml](./vars/main.yml)**

Dependencies
------------

Example Playbook
----------------

To use this role you can create a playbook such as the following (let's name it `nginx_controller_gateway.yaml` for the purposes of this example).

```yaml
- hosts: localhost
  gather_facts: no

  vars:
    controller:
      user_email: "user@example.com"
      user_password: "mySecurePassword"
      fqdn: "controller.mydomain.com"
      validate_certs: false

  tasks:
    - name: Retrieve the NGINX Controller auth token
      include_role:
        name: nginxinc.nginx-controller-generate-token

    - name: Create a gateway
      include_role:
        name: nginxinc.nginx-controller-gateway
      vars:
        # controller.auth_token: output by previous role in example
        controller.fqdn: "controller.mydomain.com"
        environmentName: "production-us-west"
        gateway:
          metadata:
            name: lending
            displayName: "Shared Public Lending BU Gateway"
            description: "Routes all non special Lending applications"
          desiredState:
            ingress:
              uris:
                "http://mortgage.acmefinancial.net":
                  {}
                "https://mortgage.acmefinancial.net":
                  {}
                "http://ratecalculator.acmefinancial.net":
                  {}
                "https://ratecalculator.acmefinancial.net":
                  {}
              tls:
                certRef:
                  ref: "/services/environments/lending-prod/certs/star.acmefinancial.net"
                protocols:
                  - "TLSv1.3"
                  - "TLSv1.2"
              placement:
                instanceRefs:
                  - ref: "/infrastructure/locations/unspecified/instances/2"
                  - ref: "/infrastructure/locations/unspecified/instances/4"
```

You can then run `ansible-playbook nginx_controller_gateway.yaml` to execute the playbook.

Alternatively, you can also pass/override any variables at run time using the `--extra-vars` or `-e` flag like so `ansible-playbook nginx_controller_gateway.yaml -e '{"controller":{ "user_email":"user@company.com","user_password":"notsecure", "fqdn": "controller.example.local", "validate_certs":false }}'`

You can also pass/override any variables by passing a `yaml` file containing any number of variables like so `ansible-playbook nginx_controller_gateway.yaml -e "@nginx_controller_gateway_vars.yaml"`

License
-------

[Apache License, Version 2.0](./LICENSE)

Author Information
------------------

[Brian Ehlert](https://github.com/brianehlert)

[Alessandro Fael Garcia](https://github.com/alessfg)

[Daniel Edgar](https://github.com/aknot242)

&copy; [NGINX, Inc.](https://www.nginx.com/) 2020
