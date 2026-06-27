# Behind a reverse proxy (HTTPS)

GOG Vault publishes **only the web UI port** (`WEB_PORT`, default `8080`) over plain HTTP, intended for a
trusted private network. To reach it securely — especially from outside your LAN — put it behind a reverse
proxy that terminates HTTPS, or access it over a VPN.

> 🔒 Don't expose the raw `:8080` port to the public internet. See [`../SECURITY.md`](../SECURITY.md).

## One thing to get right: Server-Sent Events (SSE)

The dashboard streams live progress (syncs, backups, verifies) using **Server-Sent Events** on
`/api/events`. Your proxy must **not buffer or time out** that endpoint, or progress will appear frozen.
Most modern proxies handle SSE fine; nginx needs buffering disabled on that path.

## Caddy (easiest — automatic HTTPS)

```caddy
vault.example.com {
    reverse_proxy localhost:8080
}
```

Caddy gets a certificate automatically and streams SSE correctly out of the box.

## Traefik (labels)

Point Traefik at the `web` service (or `localhost:8080`) with your usual router/TLS labels. Traefik streams
SSE by default; no special buffering config needed.

## nginx

```nginx
server {
    listen 443 ssl;
    server_name vault.example.com;

    # ssl_certificate ... ; ssl_certificate_key ... ;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Live progress stream — disable buffering / long timeout
    location /api/events {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Connection '';
        proxy_buffering off;
        proxy_cache off;
        proxy_read_timeout 24h;
    }
}
```

## Adding authentication

GOG Vault has no built-in user login (it's designed for a private network). If you proxy it somewhere
reachable, add an auth layer at the proxy — Caddy `basic_auth`, an OAuth/forward-auth middleware
(Authelia, Authentik, Traefik forward-auth), or keep it behind a VPN (WireGuard/Tailscale).
