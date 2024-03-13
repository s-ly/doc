# Docker on Linux

Update the apt package index:  
`sudo apt-get update`

Install Docker from the oﬃcial repo:  
`sudo apt-get install docker.io`

Docker version:  
`sudo docker --version`

Проверка сервера:  
`sudo docker version`

Спиок образов:  
`docker image ls`
или
`sudo docker images`

Запустить контейнер и bash в нём:  
`docker container run -it ubuntu:latest /bin/bash`

Выйти из контейнера в оболочку без остановки контейнера:  
<kbd>CTRL</kbd> + <kbd>P</kbd> + <kbd>Q</kbd>

Посмотреть процессы в докере:  
`root@6dc20d508db0:/# ps -elf`

Посмотреть запущенные контейнеры (-a даже остановленные):  
`docker container ls`
или
`docker container ls -a`

Остановить контейнер vigilant_borg:  
`docker container stop vigilant_borg`

Удалить контейнер vigilant_borg:  
`docker container rm vigilant_borg`

Создать образ из Dockerfile:  
`docker image build -t test:latest .`

Запуск web-приложения в контейнере:

```bash
docker container run -d \
--name web1 \
--publish 8080:8080 \
test:latest
```
