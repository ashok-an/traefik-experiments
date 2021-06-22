### Concepts
- configuration: static and dynamic
- providers: k8s, docker, file, swarm
- entrypoint: frontend (ip/port)
- service: backend
- routers: rules for mapping
- middlewares: filters

- traefik starts with static config and dynamic config is polled
---

### Dynamic routing
```
example:
    image: mikesir87/cats:1.0
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.example.rule=Host(`example.localhost`)"
      - "traefik.http.routers.example.entrypoints=web"
      - "traefik.http.routers.example.service=catapp"
      - "traefik.http.services.example.loadbalancer.server.port=5000"
```

### Error pages (middleware)
```
# Error Page service
  error:
    image: guillaumebriday/traefik-custom-error-pages
    labels:
          - "traefik.enable=true"
          - "traefik.http.routers.error.rule=Host(`error.localhost`)"
          - "traefik.http.routers.error.service=error"
          - "traefik.http.services.error.loadbalancer.server.port=80"
          - "traefik.http.routers.error.entrypoints=web"
     
# Error Pages Middleware
          - "traefik.http.middlewares.test-errorpages.errors.status=400-599"
          - "traefik.http.middlewares.test-errorpages.errors.service=error"
          - "traefik.http.middlewares.test-errorpages.errors.query=/{status}.html"
```

### Rate limit, compress, simple-auth
```
# Add the catapp service
  catapp:
     image: mikesir87/cats:1.0
     labels:
       - "traefik.enable=true"
  # Routers
       - "traefik.http.routers.catapp.rule=Host(`catapp.localhost`)"
       - "traefik.http.routers.catapp.service=catapp"
       - "traefik.http.routers.catapp.entrypoints=web"
       - "traefik.http.routers.catapp.middlewares=test-auth,test-compress,test-ratelimit"
       # Services
       - "traefik.http.services.catapp.loadbalancer.server.port=5000"
  # Middleware BasicAuth
       - "traefik.http.middlewares.test-auth.basicauth.users=traefik:$$apr1$$.zPbdVg8$$LcHeyCZElH.JfxkxxlMPI.,user2:$$apr1$$XDrP1Fb1$$SZqSEjvNYW44vgJgI3iEP0"
       # user1: traefik password: training
       # user2: user2 password: test123
       # Create hash password -> echo $(htpasswd -nb user2 test123) | sed -e s/\\$/\\$\\$/g
  # Compress Middleware
       - "traefik.http.middlewares.test-compress.compress=true"
  # Error Pages Middleware
       - "traefik.http.middlewares.test-errorpages.errors.status=400-599"
       - "traefik.http.middlewares.test-errorpages.errors.service=error"
       - "traefik.http.middlewares.test-errorpages.errors.query=/{status}.html"
  # Rate Limit Middleware (2 rps)
       - "traefik.http.middlewares.test-ratelimit.ratelimit.average=2"
  ```
  
  ### Observability - prometheus, access log
  ```
# traefik.yaml
#
# enable Access logs
#
accessLog: {}
#
# Configuring Multiple Filters
#
# accessLog:
#   filters:    
#     statusCodes:
#       - "404"
#     retryAttempts: true
#     minDuration: "10ms"
#
# default log level - ERROR
log:
  level: INFO

metrics:
  prometheus:
    buckets:
      - 0.1
      - 0.3
      - 1.2
      - 5.0
```

### health-check
```
# docker-compose.yaml
healthcheck:
      test: ["CMD", "traefik", "healthcheck"]
      interval: 10s
      timeout: 2s
      retries: 3
      start_period: 5s
```

## TODO
1. https, TLS, dns - need a vps with dns name
2. tracing - need instrumented application
3. LDAP auth - enterprise version?
4. ingress controller - need a kubernetes cluster
5. authz: opa - need a kubernetes cluster
  
### References:
- https://blog.thesparktree.com/traefik-advanced-config
- https://github.com/56kcloud/traefik-training
