[![Project Status: Abandoned – Initial development has started, but there has not yet been a stable, usable release; the project has been abandoned and the author(s) do not intend on continuing development.](https://www.repostatus.org/badges/latest/abandoned.svg)](https://www.repostatus.org/#abandoned)

NGINX Controller Gateway
========================

# This repository has been archived. There will likely be no further development on the project and security vulnerabilities may be unaddressed.

Upsert (create and update) gateways in NGINX Controller to support ingress for Applications and Components.

Requirements
------------

[NGINX Controller](https://www.nginx.com/products/nginx-controller/)

Role Variables
--------------

### Required Variables

`nginx_controller_fqdn` - FQDN of the NGINX Controller instance

`nginx_controller_auth_token` - Authentication token for NGINX Controller

`nginx_controller_environmentName` - Environment the gateway is associated with

`nginx_controller_gateway.metadata.name` - Name of the gateway

`nginx_controller_gateway.ingress.uris` - URI for the gateway to service

`nginx_controller_gateway.ingress.placement.instanceRefs` - Instance(s) this gateway configuration is applied to (these must exist first)

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
    nginx_controller_user_email: "user@example.com"
    nginx_controller_user_password: "mySecurePassword"
    nginx_controller_fqdn: "controller.mydomain.com"
    nginx_controller_validate_certs: false

  tasks:
    - name: Retrieve the NGINX Controller auth token
      include_role:
        name: nginxinc.nginx_controller_generate_token

    - name: Create a gateway
      include_role:
        name: nginxinc.nginx_controller_gateway
      vars:
        # controller.auth_token: output by previous role in example
        nginx_controller_environmentName: "production-us-west"
        nginx_controller_gateway:
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

Alternatively, you can also pass/override any variables at run time using the `--extra-vars` or `-e` flag like so `ansible-playbook nginx_controller_gateway.yaml -e "nginx_controller_user_email=user@company.com nginx_controller_user_password=notsecure nginx_controller_fqdn=controller.example.local nginx_controller_validate_certs=false"`

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
