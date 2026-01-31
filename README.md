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
 Попытка запуска контейнера

