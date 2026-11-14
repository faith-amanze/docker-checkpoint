\# Docker Checkpoint



Hands-on Docker work covering the CLI, Dockerfiles, volumes, networking and Docker Compose.

Tested on Windows 11, Docker Desktop, PowerShell 5.1.



\## Image sources



| Image | Source |

|---|---|

| nginx, nginx:1.27-alpine, python:3.12-slim, mysql:8.0, alpine | Docker Hub official images (https://hub.docker.com) |

| my-python-app:v1 | Built locally from part2-dockerfile/Dockerfile |

| part5-compose-backend | Built locally by Docker Compose from part5-compose/backend/Dockerfile |



\## Repo layout



\- part2-dockerfile/ : Dockerfile, app.py, requirements.txt

\- part5-compose/ : docker-compose.yml, .env, backend/ (Flask app + Dockerfile), nginx/nginx.conf

\- screenshots/ : proof for each part



\## Part 1: Docker basics



&#x20;   docker pull nginx

&#x20;   docker run -d --name my-nginx -p 8080:80 nginx

&#x20;   docker exec -it my-nginx bash

&#x20;   ls /usr/share/nginx/html

&#x20;   exit

&#x20;   docker exec my-nginx sh -c "echo '<h1>Hello from my nginx</h1>' > /usr/share/nginx/html/index.html"

&#x20;   curl.exe http://localhost:8080

&#x20;   docker restart my-nginx

&#x20;   curl.exe http://localhost:8080

&#x20;   docker stop my-nginx

&#x20;   docker rm my-nginx

&#x20;   docker rmi nginx



!\[nginx welcome](screenshots/part1-1-nginx-welcome-8080.png)

!\[restart keeps change](screenshots/part1-3-restart-keeps-change.png)



\## Part 2: Dockerfile and image



&#x20;   cd part2-dockerfile

&#x20;   docker build -t my-python-app:v1 .

&#x20;   docker run -d --name py-app -p 5000:5000 my-python-app:v1

&#x20;   curl.exe http://localhost:5000

&#x20;   docker history my-python-app:v1

&#x20;   docker rm -f py-app



!\[build finished](screenshots/part2-2-build-finished.png)

!\[run, curl, history](screenshots/part2-3-run-curl-history.png)



\## Part 3: Volumes (MySQL)



&#x20;   docker volume create mysql-data

&#x20;   docker run -d --name my-mysql -e MYSQL\_ROOT\_PASSWORD=rootpass123 -e MYSQL\_DATABASE=school -v mysql-data:/var/lib/mysql mysql:8.0

&#x20;   docker exec -it my-mysql mysql -uroot -prootpass123



Inside MySQL:



&#x20;   USE school;

&#x20;   CREATE TABLE students (id INT PRIMARY KEY AUTO\_INCREMENT, name VARCHAR(50));

&#x20;   INSERT INTO students (name) VALUES ('Ada'), ('Grace');

&#x20;   SELECT \* FROM students;

&#x20;   EXIT;



Remove the container, then start a new one on the same volume:



&#x20;   docker stop my-mysql

&#x20;   docker rm my-mysql

&#x20;   docker run -d --name my-mysql2 -e MYSQL\_ROOT\_PASSWORD=rootpass123 -v mysql-data:/var/lib/mysql mysql:8.0

&#x20;   docker exec -it my-mysql2 mysql -uroot -prootpass123 -e "SELECT \* FROM school.students;"

&#x20;   docker rm -f my-mysql2



The data persisted. The password is a throwaway demo value for local testing only.



!\[create table](screenshots/part3-3-create-table-insert-select.png)

!\[data persisted](screenshots/part3-7-data-persisted.png)



\## Part 4: Networking



&#x20;   docker network create internal-net

&#x20;   docker run -d --name web1 --network internal-net nginx

&#x20;   docker run -dit --name client --network internal-net alpine sh

&#x20;   docker exec client ping -c 3 web1

&#x20;   docker exec client apk add --no-cache curl

&#x20;   docker exec client curl http://web1

&#x20;   docker rm -f web1 client

&#x20;   docker network rm internal-net



!\[ping by name](screenshots/part4-1-network-and-ping.png)

!\[curl by name](screenshots/part4-3-curl-web1-by-name.png)



\## Part 5: Docker Compose (Flask + Nginx)



Environment variable (part5-compose/.env):



&#x20;   APP\_MESSAGE=Hello from Docker Compose



Run from scratch:



&#x20;   cd part5-compose

&#x20;   docker compose up -d --build

&#x20;   docker compose ps

&#x20;   docker compose logs

&#x20;   docker compose logs nginx

&#x20;   docker compose down



Open http://localhost to see the Flask app served through Nginx.

If port 80 is busy, change "80:80" to "8081:80" in docker-compose.yml and use http://localhost:8081.



!\[browser via nginx](screenshots/part5-2-browser-via-nginx.png)

!\[compose ps](screenshots/part5-3-compose-ps.png)

!\[compose logs](screenshots/part5-4-compose-logs.png)

!\[compose logs nginx](screenshots/part5-5-compose-logs-nginx.png)

