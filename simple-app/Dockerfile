FROM nginx:1.26.0-alpine

COPY nginx.conf /etc/nginx/nginx.conf
COPY fastcgi.conf /etc/nginx/fastcgi.conf
COPY site /opt/site
COPY site.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
