Лабораторная работа №3: Docker и контейнеризация
Выполнил: Лаптий Никита Витальевич
Группа: ИЭ-23
Дата: 27.12.2025

Задача 1: Создание кастомного образа Nginx
Цель: Создать кастомный образ Nginx с изменённой индексной страницей и загрузить его в Docker Hub.

Выполненные действия:

Установлены Docker и Docker Compose на ВМ Debian 

![Скриншот шага 1](/images/1.jpg)

docker --version && docker-compose --version
Результат: Docker версия 26.1.5, Docker Compose версия 2.26.1.

Загружен базовый образ Nginx версии 1.21.1 

![Скриншот шага 2](/images/2.jpg)

docker pull nginx:1.21.1
Результат: Образ успешно загружен (размер: 133 МБ).

Сборка кастомного образа:

![Скриншот шага 3](/images/3.jpg)

![Скриншот шага 4](/images/4.jpg)

Публикация образа в Docker Hub:

![Скриншот шага 5](/images/5.jpg)

docker push l3n1n414/custom-nginx:1.0.0

Результат: Образ успешно загружен в репозиторий Docker Hub (ссылка: https://hub.docker.com/r/l3n1n414/custom-nginx).

Задача 2: Запуск и проверка контейнера
Цель: Запустить контейнер из созданного образа, переименовать его и проверить работоспособность.

Выполненные действия:
Попытка запуска контейнера

![Скриншот шага 6](/images/6.jpg)

docker run -d --name LaptiyNikita-custom-nginx-t2 -p 127.0.0.1:8080:80 l3n1n414/custom-nginx:1.0.0

Проблема: Порт 8080 уже был занят.

Удаление старого контейнера и запуск на порту 8081 


docker rm -f e5aa71c1384f
docker run -d --name LaptiyNikita-custom-nginx-t2 -p 127.0.0.1:8081:80 l3n1n414/custom-nginx:1.0.0

Результат: Контейнер успешно запущен.

Переименование контейнера 

docker rename LaptiyNikita-custom-nginx-t2 custom-nginx-t2

Проверка содержимого веб-страницы

docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html

docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html

**Результат: Страница доступна, содержимое соответствует файлу index.html.**

![Скриншот шага 7](/images/7.jpg)

Задача 3: Работа с запущенным контейнером
Цель: Изучить управление контейнером, изменить его конфигурацию и проанализировать проблемы.

Выполненные действия:

Подключение к контейнеру и изменение конфигурации Nginx

docker exec -it custom-nginx-t2 bash
nano /etc/nginx/conf.d/default.conf

Изменён порт Nginx с 80 на 81.

Перезагрузка Nginx внутри контейнера

nginx -s reload

![Скриншот шага 9](/images/9.jpg)

Проверка доступности портов внутри контейнера

Порт 80 недоступен:
curl http://127.0.0.1:80

Порт 81 работает:
curl http://127.0.0.1:81

Проблема: Проброс порта 8081 хоста на порт 80 контейнера перестал работать, так как Nginx теперь слушает порт 81.

Удаление контейнера:

docker rm -f custom-nginx-t2

![Скриншот шага 10](/images/10.jpg)

Задача 4: Работа с томами (Volumes)
Цель: Продемонстрировать работу с томами Docker, создав общую папку между контейнерами и хостом.

Выполненные действия:

Запуск контейнеров CentOS и Debian с общим томом

docker run -d --name centos-vol -v $(pwd):/data centos:7 sleep infinity
docker run -d --name debian-vol -v $(pwd):/data debian:latest sleep infinity

Создание файлов внутри контейнеров и на хосте

docker exec centos-vol bash -c 'echo "Файл создан внутри CentOS" > /data/from_centos.txt'
echo "Файл создан на хосте" > /data/from_host.txt

Проверка доступности файлов:

Файлы, созданные в контейнерах и на хосте, отображаются в общей директории /data.

![Скриншот шага 11](/images/11.jpg)

Задача 5: Docker Compose и Portainer
Цель: Настроить Docker Compose, локальный реестр образов и управление через Portainer.

Выполненные действия:

Создание и запуск Docker Compose-проекта 

![Скриншот шага 12](/images/12.jpg)

nano compose.yaml
docker compose up -d

Результат: Запущены сервисы registry:2 (порт 5000) и portainer/portainer-ce (управление через веб-интерфейс).

Загрузка кастомного образа в локальный registry 

docker tag l3n1n414/custom-nginx:1.0.0 127.0.0.1:5000/custom-nginx:latest
docker push 127.0.0.1:5000/custom-nginx:latest

![Скриншот шага 13](/images/13.jpg)

Анализ контейнера через docker inspect

![Скриншот шага 14](/images/14.jpg)

![Скриншот шага 15](/images/15.jpg)

docker inspect custom-nginx-t2

Результат: Получена детальная информация о контейнере (конфигурация, сеть, тома и т.д.).

Управление контейнерами через Portainer:

Настройка окружения через веб-интерфейс https://127.0.0.1:9000.

Деплой стека с Nginx из локального registry.
