**Домашнее задание №14**

**Тема** ***"Docker"***

**Задача**
1. Установите Docker Compose - как плагин, или как отдельное приложение
2. Создайте свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)
3. Определите разницу между контейнером и образом
4. Вывод опишите в домашнем задании.
5. Ответьте на вопрос: Можно ли в контейнере собрать ядро?

**Результат работы**
1. Создан Vagrantfile для работы с Docker:
```
vagrant ssh 
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.0-71-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Jun 25 16:27:25 UTC 2024

  System load:  0.05126953125     Users logged in:          0
  Usage of /:   7.5% of 38.70GB   IPv4 address for docker0: 172.17.0.1
  Memory usage: 37%               IPv4 address for enp0s3:  10.0.2.15
  Swap usage:   0%                IPv4 address for enp0s8:  192.168.11.150
  Processes:    101


Expanded Security Maintenance for Applications is not enabled.

88 updates can be applied immediately.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


*** System restart required ***
Last login: Tue Jun 25 14:41:57 2024 from 10.0.2.2
```

2. Создан ansible playbook `docker_install.yml`, предназначенный для установки Docker и Docker-compose на удаленный хост.
При выполнении команды `ansible-playbook docker_install.yaml` на удаленный хост будет установлен Docker: 
``` 
root@docker:/home/vagrant# docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED       STATUS       PORTS                                     NAMES
20051149777b   nginx_test:v1.0   "nginx -g 'daemon of…"   2 hours ago   Up 2 hours   443/tcp, 8080/tcp, 0.0.0.0:8080->80/tcp   nginx
root@docker:/home/vagrant#
```

3. Создан ansible playbook `build_nginx.yml`, предназначенный для сборки кастомного образа nginx.
При выполнении команды `ansible-playbook build_nginx.yaml`, будет выполнено:
- Создание дирректории для сборки
- Загрузка образа nginx
- Копирование исходных файлов (`Dockerfile` и `index.html`)
- Сборка образа контейнера
- Запуск контейнера

В результате будет развернут кастомный контейнер nginx.
Проверена доступность:
```
root@docker:/home/vagrant# curl http://192.168.11.150:8080
<!doctype html>
<html>
 <body style="backgroud-color:rgb(49, 214, 220);"><center>
    <head>
     <title>Docker Project</title>
    </head>
    <body>
     <p>Welcome to my Docker Project!<p>
        <p>Today's Date and Time is: <span id='date-time'></span><p>
        <script>
             var dateAndTime = new Date();
             document.getElementById('date-time').innerHTML=dateAndTime.toLocaleString();
        </script>
        </body>
</html>root@docker:/home/vagrant#
```

4. Собранный контейнер загружен в Docker hub: https://hub.docker.com/repository/docker/freemax0602/docker_test/

5. Разница между контейнером и образом: 
Образ - это неизменяемый файл, содержащий исходный код, библиотеки, зависимости, инструменты и другие файлы, необходимые для запуска приложения.
Так как образы являются просто шаблонами, их нельзя создавать или запускать. Этот шаблон можно использовать в качестве основы для построения контейнера. 
Контейнер - это, в конечном счете, просто образ. При создании контейнера поверх образа добавляет слой, доступный для записи, что позволяет менять его по своему усмотрению.

6. Можно ли в контейнере собрать ядро?
В Docker-контейнере можно собрать ядро с произвольными патчами, флагами конфигурации и тегом, например, repository на сайте Docker Hub для сборки ядра Debian по ссылкам:
- https://hub.docker.com/r/docker/for-desktop-kernel/tags
- https://github.com/rabilrbl/kernel-build


