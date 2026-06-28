# Caddy Docker Proxy
Deployment files for caddy docker proxy used as ingress for docker services.

## Getting started
1. Create an environment file named `override.env``
```bash
ACME_SERVER=https://acme.example.com
ACME_EMAIL=email@example.com
```

2. Create a file named `cert_volumes.yml` for wildcard certificates
```yaml
services:
  caddy:
    volumes:
      - /etc/ssl/example.com:/certs/example.com:ro
```

3. Create the caddy docker network
```bash
docker network create caddy
```

4. Run the proxy
```bash
docker compose up -d
```

## Configuring Docker Compose files to use Caddy
1. add labels for caddy to generate a reverse proxy configuration
```yaml
services:
  my_app:
    labels:
      caddy: myapp.example.com
      caddy.reverse_proxy: "http://myapp:3000"

      # If you dont want to use ACME and expose your certificate publicly, you can use a wildcard certificate mounted in caddy.
      caddy.tls: /certs/example.com/fullchain.pem /certs/example.com/privkey.pem

      # You can also add basic authentication
      caddy.basic_auth: "/*"
      caddy.basic_auth.loki: "$$2a$$14$$..." # Generate using docker exec -it caddy caddy hash-password --plaintext "yourpassword"
```

2. Add the caddy network
```yaml
services:
  my_app:
    networks:
      - caddy

networks:
  caddy:
    external: true
```

