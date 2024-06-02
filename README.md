# docker-study

### Различные Dockerfile
1. Простой сайт, собираемый вместе с nginx и настройками. [Dockerfile](simple-app/Dockerfile)
2. Django. [Dockerfile](django/Dockerfile)
3. Кастомная сборка [nginx-proxy](https://github.com/nginx-proxy/nginx-proxy), в которой дополнительно установлен модуль [Brotli](https://github.com/google/ngx_brotli). [Dockerfile](nginx-proxy/Dockerfile)

### Docker compose
В файле [docker-compose](docker-compose.yml) используются разные подходы.

- Использование готового образа docker-контейнера, собранного ранее
```yaml
  app1:
    container_name: simple-app
    restart: always
    image: ghcr.io/dens-al/docker-study/simple-app:main
    environment:
      VIRTUAL_HOST: app.den-ops.com
      VIRTUAL_PATH: /
      VIRTUAL_PORT: 80
      LETSENCRYPT_HOST: app.den-ops.com
```
Переменные окружения `VIRTUAL_HOST`, `VIRTUAL_PATH`, `VIRTUAL_PORT` используются контейнером nginx-proxy для автоматической настройки. Подробнее [тут](https://github.com/nginx-proxy/nginx-proxy/tree/main/docs).
- Использование внешнего приложения (вспомогательного)
```yaml
  dozzle:
    container_name: dozzle
    restart: always
    image: amir20/dozzle:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      DOZZLE_BASE: /logs      
      VIRTUAL_HOST: app.den-ops.com
      VIRTUAL_PATH: /logs
      VIRTUAL_PORT: 8080
```
- Сборка приложения через указание на папку, содержащую Dockerfile
```yaml
  nginx-proxy:
    container_name: nginx-proxy
    restart: always
    build: nginx-proxy  # my folder with custom Dockerfile for nginx-proxy
    ports:
      - 80:80           # must be for acme challenge to approve LetsEncrypt domain
      - 443:443
    volumes:
      - /opt/nginx/certs:/etc/nginx/certs # must contain valid SSL certs and key with domain name e.g. den-ops.ru.crt and den-ops.ru.key
      - /opt/static:/mount/app/static     # added for django static files. Defined in vhost.d/default
      - html:/usr/share/nginx/html        # must be for acme challenge to approve LetsEncrypt domain
      - vhost:/etc/nginx/vhost.d
      - /var/run/docker.sock:/tmp/docker.sock:ro
    environment:
      LOG_JSON: true
      LOG_FORMAT: '{"msec":"$msec","connection":"$connection","connection_requests":"$connection_requests","pid": "$pid","request_id": "$request_id","request_length": "$request_length","remote_addr": "$remote_addr","remote_user": "$remote_user","remote_port": "$remote_port","time_local": "$time_local","time_iso8601": "$time_iso8601","request": "$request","request_uri": "$request_uri","args": "$args","status": "$status","body_bytes_sent": "$body_bytes_sent","bytes_sent": "$bytes_sent","http_referer": "$http_referer","http_user_agent": "$http_user_agent","http_x_forwarded_for": "$http_x_forwarded_for","http_host": "$http_host","server_name": "$server_name","request_time": "$request_time","upstream": "$upstream_addr","upstream_connect_time": "$upstream_connect_time","upstream_header_time": "$upstream_header_time","upstream_response_time": "$upstream_response_time","upstream_response_length": "$upstream_response_length","upstream_cache_status": "$upstream_cache_status","ssl_protocol": "$ssl_protocol","ssl_cipher": "$ssl_cipher","scheme": "$scheme","request_method": "$request_method","server_protocol": "$server_protocol","pipe": "$pipe","gzip_ratio": "$gzip_ratio" }'
```

