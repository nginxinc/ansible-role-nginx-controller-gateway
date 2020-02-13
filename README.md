NGINX Controller Gateway
=========

Upsert (create and update) gateways in NGINX Controller to support ingress for Applications and Components.

Requirements
------------

Role Variables
--------------

Required:
controller_fqdn
environment_name
gateway.name
ingress.uris
ingress.placement

```yaml
controller_fqdn:   # fqdn of the controller instance
environment_name:  # environment the gateway is associated with

gateway:
  name: # REQUIRED: name of the gateway
  displayName: # friendly display name
  description: # complete sentence description
  instance_refs: # REQUIRED: instance(s) this gateway configuration is applied to (this must exist first)
  certificate_ref: # optional: required if https/tls (this must exist first)
  tags: # optional: array of single word tags
  - tag1
  - tag2

ingress:
  uris: # array of URIs
  - ingress_uri: # REQUIRED: one URI for the gateway to service http://www.example.com
      matchMethod: PREFIX #optional: PREFIX,REGEX,REGEX_CASE_SENSITIVE,SUFFIX,EXACT
      tls: #optional section: per-DNS certificate for URI routing
        certRef:
          ref: #optional: reference to Controller Certificate configuration
        protocols: # array: TLSv1,TLSv1.1,TLSv1.2,TLSv1.3,SSLv2,SSLv3
        cipher: #cipher strength
        preferServiceCipherServiceConfigState: # ENABLED,DISABLED
        sessionCache: # OFF,NONE,BUILTIN,SHARED
  methods: #optional array: POST,GET,PUT,DELETE,PATCH,HEAD,TRACE,OPTIONS,CONNECT
  headers: #optional section applies to all URIs
  - name: string
    nameMatchMethod: #PREFIX,REGEX,REGEX_CASE_SENSITIVE,SUFFIX,EXACT
  - value: string
    valueMatchMethod: #PREFIX,REGEX,REGEX_CASE_SENSITIVE,SUFFIX,EXACT
  http2: #optional applies to all URIs: ENABLED,DISABLED
  spdy: #optional applies to all URIs: ENABLED,DISABLED
  proxyProtocol: #optional applies to all URIs: ENABLED,DISABLED
  setFib: #optional applies to all URIs: int
  fastOpen: #optional applies to all URIs: int
  backlog: #optional applies to all URIs: int
  receiveBufferSize: #optional applies to all URIs: string
  sendBufferSize: #optional applies to all URIs: string
  acceptFilter: #optional applies to all URIs: DATA_READY,HTTP_READY
  deferred: #optional applies to all URIs: ENABLED,DISABLED
  isIpv6Only: #optional applies to all URIs: bool
  reusePort: #optional applies to all URIs: ENABLED,DISABLED
  tls: #optional: applies to all URIs
    certRef:
      ref: #optional: reference to Controller Certificate configuration
    protocols: # array: TLSv1,TLSv1.1,TLSv1.2,TLSv1.3,SSLv2,SSLv3
    cipher: string
    preferServiceCipherServiceConfigState: # ENABLED,DISABLED
    sessionCache: # OFF,NONE,BUILTIN,SHARED
  tcpKeepAlive: #optional section
    idle: string
    interval: string
    count: 0
  placement: # REQUIRED
    instanceRefs:
    - ref: "{{instance_ref}}"
```

Dependencies
------------

Example Playbook
----------------

```yaml
- hosts: localhost
  gather_facts: no

  tasks:
  - include_role:
      name: nginx_controller_generate_token
    vars:
      user_email: "user@example.com"
      user_password: 'secure_password'
      controller_fqdn: "controller.example.local"

  - name: install the agent
    include_role:
      name: nginx_controller_gateway
    vars:
      controller_fqdn: "controller.example.local"
      environment_name: "production-us-west"
      # controller_auth_token: output by previous Role in example
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
          - "TLSv1.2
        placement:
          instanceRefs:
          - "/infrastructure/locations/unspecified/instances/2"
          - "/infrastructure/locations/unspecified/instances/4"

  ```

License
-------

Apache-2.0

Author Information
------------------

brianehlert
