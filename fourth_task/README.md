# Задание:

 Установить докер. Создать докер-контейнер с убунту, с помощью run/exec внутри контейнера установить ssh-сервер. Настроить ssh на нестандартном порту внутри контейнера (7770). Настроить вход по приватному публичному ключам. Зайти по ssh внутрь контейнера по приватному ключу.doc

# Решение:

- Установка и запуск контейнера:  
`docker pull ubuntu`  
`docker run -d -t --name ubuntu -p 7770:7770 ubuntu`  
- Установка shh внутри контейнера:  
`docker exec ubuntu apt update`  
`docker exec ubuntu apt install  openssh-server sudo -y ssh`  
- Меняем конфиг ssh:  
`docker exec ubuntu sed -i 's/#Port 22/Port 7770/' /etc/ssh/sshd_config`  
- Создаем ssh папку:  
`docker exec ubuntu mkdir root/.ssh`  
- Генерим ключи:  
`ssh-keygen -t ed25519`  
- Копируем public на контейнер:  
`docker cp ~/.ssh/id_ed25519.pub ubuntu:root/.ssh/authorized_keys`  
`docker exec ubuntu chmod 600 root/.ssh/authorized_keys`  
- Рестарт ssh сервиса:  
`docker exec ubuntu service ssh restart`  
- Узнаем адрес контейнера:  
`docker inspect -f "{{ .NetworkSettings.IPAddress }}" ubuntu`  
- Подключаемся по ssh:  
`ssh root@<адрес из пункта выше> -p 7770`  