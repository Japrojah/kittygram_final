# Kittygram 
Приложение для публикации фотографий домашних питомцев
Автор: Дима(github.com/Japrojah)

Описание проекта
--------------
Реализованны возможности публикации постов с фотографиями,
кличкой, цветом, годом рождения и достижениями питомцев.
Достижения питомцев можно создавать самостоятельно.
Цвета шерсти питомцев можно выбрать из предустановленного списка.
# В проекте были сипользованы: 
Django REST
Python 3.9/python 3.9-slim
Gunicorn
Nginx
JS
Node.js
PostgreSQL
Docker

# Как запустить проект:
sudo apt update
sudo apt install curl
curl -fSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
sudo apt-get install docker-compose-plugin;
### Переходим в директорию проекта.
cd kittygram_final/
### Создаем и редактируем файл .env, в котором нужно указать данные
sudo nano .env
### 
DJANGO_KEY=django-insecure-cg6*%6d51ef8f#4!r3*$vmxm4)abgjw8mo!4y-q*uq1!4$-88$
POSTGRES_DB=<Желаемое_имя_базы_данных>
POSTGRES_USER=<Желаемое_имя_пользователя_базы_данных>
POSTGRES_PASSWORD=<Желаемый_пароль_пользователя_базы_данных>
DB_HOST=db
DB_PORT=5432
### Сохраняем файл. Ключ DJANGO_KEY рекомендуется заменить
### Далее выполняем последовательно
sudo docker compose -f docker-compose.production.yml pull
sudo docker compose -f docker-compose.production.yml down
sudo docker compose -f docker-compose.production.yml up -d
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collect_static/. /static_backend/static/
### Создаем суперпользователся. Следуем инструкциям при выполнении.
sudo docker compose -f docker-compose.production.yml exec backend python manage.py createsuperuser

### Генерируем новый секретный ключ Django
sudo docker compose -f docker-compose.production.yml exec backend python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
### В ответ будет примерно такая строка: cg6*%6d51ef8f#4!r3*$vmxm4)abgjw8mo!4y-q*uq1!4$-88$
### Вставляем в .env файл. Должно получиться примерно такое:
DJANGO_KEY=django-insecure-cg6*%6d51ef8f#4!r3*$vmxm4)abgjw8mo!4y-q*uq1!4$-88$

# Установите веб-сервер, мы рекомендуем Nginx:
sudo apt install nginx -y
### Запускаем
sudo systemctl start nginx
### Настраиваем firewall
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
### Включаем firewall
sudo ufw enable
### Открываем конфигурационный файл NGINX
sudo nano /etc/nginx/sites-enabled/default

## В Docker-compose проекта уже описан запуск контейнера с "внутренним" Nginx, так что настройке внешний таким образом: 

server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_set_header HOST $host;
        proxy_pass http://127.0.0.1:9000;

    }
}

### Сохраняем изменения и выходим из редактора
### Проверяем корректность настроек
sudo nginx -t
###Запускаем NGINX
sudo systemctl start nginx

