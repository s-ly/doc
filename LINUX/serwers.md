https://vds.selectel.ru/

имя: web
логин: root
пароль: ed55vfmnu0
IP: 81.163.31.153
SSH: ssh root@81.163.31.153

Скопировать всю папку DanceHouseBot на сервер в корень:
scp -r /home/sergey/DanceHouseBot root@81.163.31.153:/root/

Скопировать все файлы из папки DanceHouseBot на сервер в папку DanceHouseBot:
scp /home/sergey/DanceHouseBot/* root@81.163.31.153:/root/DanceHouseBot/

