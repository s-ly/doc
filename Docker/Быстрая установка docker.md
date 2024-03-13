Да, для установки Docker на Ubuntu:

`curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh`

Проверить статус службы Docker:

`sudo systemctl status docker`

для запуска команд Docker без sudo:

`sudo usermod -aG docker $USER`
