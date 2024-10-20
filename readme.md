1.	Создание контейнера nginx, создание контейнера с WSGI-сервером на Python (с помощью docker run ...). 
1)	Запускаем контейнер на основе образа nginx, сопоставляя локальную папку content с некоторым каталогом внутри самого контейнера (создание тома) с помощью -v. ro – readonly – чтобы из контейнера контент был доступен только для чтения. -d - запуск в демон режиме. -p сопоставляем порт на хостовой машине и в контейнере. docker run --name my-first-website -v C:\Users\uchen\Desktop\content:/usr/share/nginx/html:ro -d -p 8000:80 nginx

2)	Запускаем контейнер на основе образа python также сопоставляя хостовую директорию и некоторый каталог внутри контейнера в режиме demon: docker run --name python-web-service -v C:\Users\uchen\Desktop\content:/root/content:ro -d -p 8001:80 python python3 -m http.server --directory /root/content --bind 0.0.0.0 80 


2.	1) Создание докер образа на основе nginx с помощью Dockerfile с инструкциями
FROM nginx
VOLUME ["/usr/share/nginx/html"]
COPY . /usr/share/nginx/html
EXPOSE 80
с помощью команды docker build -t my-nginx:0.1 .
Далее проходим процесс аутентификации с помощью команды: docker login
И пушим в удаленный реестр: docker tag my-nginx:0.1 olivka07/my-nginx:latest && docker push olivka07/my-nginx:latest

![image](https://github.com/user-attachments/assets/493864b4-b085-4c6f-9399-4a94cf597ac3)

 
2)	создание докер образа на основе python c WSGI-сервером на Python с помощью Dockerfile с инструкциями
FROM python:3.12-alpine
WORKDIR /content/root
VOLUME ["/content/root"]
COPY . /content/root
EXPOSE 80
CMD ["python3", "-m", "http.server", "--bind", "0.0.0.0", "80"]
с помощью команды docker build -t my-wsgi-server:0.1 .
Далее проходим процесс аутентификации с помощью команды: docker login
И пушим в удаленный реестр: docker tag my-wsgi-server:0.1 olivka07/my-wsgi-server:latest && docker push olivka07/my-wsgi-server:latest

 ![image](https://github.com/user-attachments/assets/c0794939-1559-4956-928f-f88c548090a5)

![image](https://github.com/user-attachments/assets/30cba33b-4546-40f6-bd2d-0f3370923756)


### Образ с nginx https://hub.docker.com/r/olivka07/my-nginx
### Образ с wsgi-server https://hub.docker.com/r/olivka07/my-wsgi-server

3. Был добавлен файл docker-compose.yml для управления несколькими образами и контейнерами одновременно со следующим содержимым:
   ```
   services:
    nginx:
      build: content/
      ports:
        - 8081:80
    wsgi:
      build: python_content/
      ports:
        - 8082:80
      restart: always
   ```

   Dockerfile для nginx представлен в папке content, а для wsgi - python_content
   команда для запуска контейнеров: docker-compose up
