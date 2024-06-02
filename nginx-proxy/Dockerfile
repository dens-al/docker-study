# Build necessary modules for Nginx
FROM ubuntu:22.04 as builder

RUN apt update \
    && apt upgrade -y \
    && apt install -y libpcre3 libpcre3-dev zlib1g zlib1g-dev openssl libssl-dev wget git gcc make libbrotli-dev libmaxminddb0 libmaxminddb-dev mmdb-bin

WORKDIR /app
# Build Brotli module from Google
RUN wget https://nginx.org/download/nginx-1.25.4.tar.gz && tar -zxf nginx-1.25.4.tar.gz
RUN git clone --recurse-submodules -j8 https://github.com/google/ngx_brotli
RUN cd nginx-1.25.4 && ./configure --with-compat --add-dynamic-module=../ngx_brotli \
    && make modules \

# Build GeoIP2 module
RUN git clone https://github.com/leev/ngx_http_geoip2_module.git
RUN cd nginx-1.25.4 && ./configure --with-compat --add-dynamic-module=../ngx_http_geoip2_module \
    && make modules

#CMD ["sleep", "3600"] # for debugging

# Use nginx-proxy as base pre-configured proxy https://github.com/nginx-proxy/nginx-proxy
FROM nginxproxy/nginx-proxy:1.5.1
# Contains nginx 1.25.4 which must be the same version for compiled brotli modules

RUN apt-get update \
    && apt-get install -y --no-install-recommends --no-install-suggests libmaxminddb0 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /app/nginx-1.25.4/objs/ngx_http_brotli_static_module.so /etc/nginx/modules/
COPY --from=builder /app/nginx-1.25.4/objs/ngx_http_brotli_filter_module.so /etc/nginx/modules/
COPY --from=builder /app/nginx-1.25.4/objs/ngx_http_geoip2_module.so /etc/nginx/modules/
RUN echo "load_module modules/ngx_http_brotli_filter_module.so;\nload_module modules/ngx_http_brotli_static_module.so;\nload_module modules/ngx_http_geoip2_module.so;\n$(cat /etc/nginx/nginx.conf)" > /etc/nginx/nginx.conf
COPY brotli.conf /etc/nginx/conf.d/brotli.conf
COPY nginx-logs.conf /etc/nginx/conf.d/nginx-logs.conf

COPY vhost.d/default /etc/nginx/vhost.d/default
COPY custom.conf /etc/nginx/conf.d/custom.conf
