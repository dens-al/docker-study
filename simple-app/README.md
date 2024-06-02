# Simple site

### Образ доступен по адресу:

```sh
registry.infernofeniks.ru/feniks/nginx-feniks:latest
```

Запускаем контейнер:
```sh
docker run -d --name feniks-web -p 80:80 registry.infernofeniks.ru/feniks/nginx-feniks:0.0.1
```

Проверяем контейнер:
```sh
docker ps -a
```
```
CONTAINER ID   IMAGE                                                 COMMAND                  CREATED         STATUS         PORTS                               NAMES
0f813d388323   registry.infernofeniks.ru/feniks/nginx-feniks:0.0.1   "/docker-entrypoint.…"   9 minutes ago   Up 9 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp   feniks-web
```
```sh
docker images
```
```
REPOSITORY                                      TAG       IMAGE ID       CREATED             SIZE
registry.infernofeniks.ru/feniks/nginx-feniks   0.0.1     536351b3b538   About an hour ago   48.9MB
registry.infernofeniks.ru/feniks/nginx-feniks   latest    536351b3b538   About an hour ago   48.9MB
```

Проверяем работу приложения:
`http://<адрес_хоста>/`

<img src = "image_1.jpg" width = 50%>
