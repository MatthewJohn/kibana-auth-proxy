# kibana-auth-proxy

Designed to work as a forwardAuth proxy for Authelia (possibly others, like nginx, not tested) in order to use LDAP/Active Directory for user access in Elasticsearch without paid subscription.

1. Request goes to Traefik
2. Traefik prxies it to Authelia in order to verify user
3. If it receives `200` forwards headers from Authelia to second auth -> kibana-auth-proxy
4. kibana-proxy-auth:
  - generates random password for local Kibana user (has nothing to do with LDAP password)
  - uses information from Authelia headers to create/update local user in Kibana + AD group/kibana roles mappings from config file
  - generates and passes back to Traefik header:
     ```
     Authorization: Basic XXXYYYZZZZ
     ```
5. Traefik passes user to Kibana with `Authorization` header which has password already set by kibana-proxy-pass and logs him/her in :)
6. passwords are meant to have short time span of life and are regenerated transparently for user while using Kibana

Headers used by Authelia and kibana-auth-proxy:
```
remote-email
remote-groups
remote-name
remote-user
```

![alt text](https://github.com/wasilak/kibana-auth-proxy/blob/main/kibana-auth-proxy.png?raw=true)
