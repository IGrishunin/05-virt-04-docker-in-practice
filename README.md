# 05-virt-04-docker-in-practice
**Задача 0**
Выполнил команду ```docker compose version```
Получил вывод
```Docker Compose version 5.1.3.astra1```

**Задача 1**
Необходимые файлы добавлены в форк

**Задача 2**
К сожалению с Яндексом проблемы, сканировать уязвимости он отказывается, поэтому прилаживаю просто скрин залитого образа

<img width="771" height="900" alt="image" src="https://github.com/user-attachments/assets/4ede2357-2f75-48da-98b8-9c9ce880c9fd" />


**Задача 3**
Необходимые файлы приложены в форке
<img width="838" height="773" alt="Задача3" src="https://github.com/user-attachments/assets/a64c3706-3445-4f4e-84e7-cc29c337f5ea" />

**Задача 4**
Листинг script.sh
```
#!/bin/bash

source ./.env

apt update && apt install docker-compose-v2 git || exit 1

if [[ ! -d /opt/project ]]; then
        git clone https://github.com/IGrishunin/shvirtd-example-python.git /opt/project && cd /opt/project || exit 1
else
        echo "Репозиторий уже склонирован"
        exit 0
fi

docker compose up -d || exit 1
```
Настроил удалённый контекст docker командой
```
docker context create yc-server --docker "host=ssh://igrishunin@158.160.156.2"
```
Переключился на контекст```docker context use yc-server```
выполнил ```docker context ls```
Получил вывод
```
NAME          DESCRIPTION                               DOCKER ENDPOINT                      ERROR
default       Current DOCKER_HOST based configuration   unix:///var/run/docker/docker.sock   
yc-server *                                             ssh://igrishunin@158.160.156.2
```
Далее выполнил ```docker ps -a```
Получил вывод
```
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                      NAMES
8ab0402ad77e   nginx:latest   "/docker-entrypoint.…"   7 minutes ago   Up 7 minutes                              project-ingress-proxy-1
68c6dc956aff   haproxy:2.4    "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   127.0.0.1:8080->8080/tcp   project-reverse-proxy-1
ab22712f221c   mysql:8        "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes   3306/tcp, 33060/tcp        db
0c130cbfcbfe   project-web    "uvicorn main:app --…"   7 minutes ago   Up 7 minutes                              web
```

Скрин SQL запроса с ВМ Яндекса

<img width="686" height="657" alt="Задача4" src="https://github.com/user-attachments/assets/69a9ef2c-b51d-4d6d-9afd-859c589217de" />

**Задача 6**
Выполнил ```dive hashicorp/terraform:latest```

<img width="1918" height="876" alt="Задача6" src="https://github.com/user-attachments/assets/2b13c0dc-5c4f-4ab6-9c2e-a58717dcd1d0" />

Сохранил образ командой  ```docker save -o terraform.tar hashicorp/terraform:latest```
Распаковал ```tar -xvf terraform.tar```
Вывод
```
blobs/
blobs/sha256/
blobs/sha256/2d664eecf5e7d017570e58c8cec8a8a65adff55b713f5f370bfddd22a0698cb6
blobs/sha256/6021ecbea249529977d20d05f3efa2fc204aedcc03b629cc741fbdbb144de677
blobs/sha256/8cffe811b97bb7db1b8aa14eff94b97f1095eb3394bdd03b3e916249c28091f5
blobs/sha256/a5d5a5a892c98f60fcbfa81eac2a40fc19e7b3fa7719bccae10ff6111d22524a
blobs/sha256/a90a488ef907329f27fb83f3b4400e5ca10ae8b73bb3073450c2203fbfa5d12f
blobs/sha256/b31c7f8fb200e48c18f2f1f189def5cd5d972a673186cf3bee026ac9071c2d80
blobs/sha256/ee2e0f259e55cf6b05037721ead2793b146517cb68820e0a65982c0b37ee12c2
index.json
manifest.json
oci-layout
```
Но хеши не совпадают с тем, что указано в dive.
Требуется хеш ```sha256:7cfe271fdfc46518918bc2fff3c5afa54ff86777157e419a2cf980f760b56f5d```
Информацию в интернете не удалось найти, как конкретно сопоставить слои используя только dive и docker save без использования inspect и вычисления нужного слоя по размеру(одни костыли)
Надеюсь, что подскажите, как с этим разобраться
