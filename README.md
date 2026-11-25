mkdir -p /c/DockerProjects/custom-nginx
cd /c/DockerProjects/custom-nginx


FROM redis:latest
CMD ["redis-server"]

FROM tomcat:9.0-jdk17-temurin
COPY app/target/*.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
CMD ["catalina.sh", "run"]


FROM python:3.12-alpine
WORKDIR /app
COPY app.py .
EXPOSE 5000
CMD ["python", "app.py"]


FROM nginx:latest
COPY index.html /usr/share/nginx/html/

docker build -t carimage1234 . 
docker images
docker run -d --name carcontainer -p 8080:8080 carimage1234
docker ps
docker login
docker tag carimage1234 chandrayani2007/carimage1234:latest
docker push chandrayani2007/carimage1234:latest

FROM openjdk:17
WORKDIR /app
COPY Hello.java .
RUN javac Hello.java
CMD ["java", "Hello"]

docker --version
docker pull mysql:8.0
docker pull wordpress:latest
mkdir Library-Management
cd Library-Management
notepad docker-compose.yml
docker network create library-network
docker compose up -d
docker compose ps

services:
  db:
    image: mysql:8.0
    container_name: library-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root123
      MYSQL_DATABASE: library
      MYSQL_USER: admin
      MYSQL_PASSWORD: admin123
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - library-network
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-uroot", "-proot123"]
      interval: 10s
      timeout: 5s
      retries: 5

  wordpress:
    image: wordpress:latest
    container_name: library-wordpress
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: admin
      WORDPRESS_DB_PASSWORD: admin123
      WORDPRESS_DB_NAME: library
    depends_on:
      db:
        condition: service_healthy
    networks:
      - library-network

volumes:
  db_data:

networks:
  library-network:
    external: true

