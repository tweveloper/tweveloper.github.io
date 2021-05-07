---
layout: post
title:  "개발환경 구성"
date:   2021-05-07
categories: envops
tags: envops docker ubuntu linux nginx .netcore postgresql reactjs
comments: false
---

Docker + Ubuntu + Nginx + .NETCore + ReactJS + PostgreSQL  
위와 같이 개발환경을 구성해보자.

1. Docker에 Ubuntu 컨테이너를 실행한다.

```
SSH 접속
docker images
```
![docker images](/assets/img/envops/ubuntu-nginx/docker-images.png)
```
docker run -it -p 8081:80 --name=ubuntu_nginx ubuntu
port를 미리 바인딩 해준다.
```
![docker run](/assets/img/envops/ubuntu-nginx/docker-run.png)
![docker ps](/assets/img/envops/ubuntu-nginx/docker-ps.png)

docker가 중지 되었다면
```
docker start ubuntu_nginx
docker exec -it ubuntu_nginx /bin/bash
```
2. Nginx를 설치한다.
    - Nginx 설치 전 apt 명령어 실행
    ```
    apt update
    apt upgrade -y
    apt autoremove -y
    apt install sudo
    sudo sed -i 's/kr.archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list
    apt install nano vim git
    apt install systemd
    ```
    - Nginx 설치
    ![install nginx](/assets/img/envops/ubuntu-nginx/apt-install-nginx.PNG)
    ```
    apt install nginx
    sudo service nginx status
    sudo nginx -t
    sudo service nginx restart
    ```
3. PostgreSQL 설치한다.
    ```
    apt install postgresql postgresql-contrib
    sudo service postgresql status
    sudo service postgresql start

    -- postgres user로 접근
    sudo -i -u postgres
    -- psql db로 접근
    psql

    CREATE USER bcadmin WITH PASSWORD '1234';
 
    -- 새 데이터베이스(bc_db)의 소유자(bcadmin)가 되게하여 직접 구성하고 관리
    CREATE DATABASE bc_db OWNER bcadmin;
    
    CREATE SCHEMA bcpaltform AUTHORIZATION bcadmin;

    -- public 스키마가 아닌 별도의 스키마를 생성해서 사용하려면 search_path 를 잡아주는 것이 편하다.
    -- search_path는 탐색할 스키마의 순서를 지정해주는 변수다.
    -- show search_path;
    alter USER bcadmin set search_path = 'bcpaltform';
    
    exit
    exit

    ```
    - bcadmin으로 접근하여 테이블을 시퀀스, 테이블을 만들어 보자
    ```
    --bcadmin 계정으로 bc_db 데이터베이스에 접근 / 비번 1234
    psql -h localhost -U bcadmin -d bc_db

    --스키마 생성
    CREATE SCHEMA bcpaltform AUTHORIZATION bcadmin;

    --시퀀스 생성
    CREATE SEQUENCE "menu_id_seq";

    --메뉴 생성(id는 위에만든 시퀀스 기반)
    CREATE TABLE "menu" (
        "id" INTEGER NOT NULL DEFAULT nextval('menu_id_seq'::regclass), "label" VARCHAR(255) NOT NULL,
        "icon" VARCHAR(50) NULL DEFAULT NULL,
        "link_to" VARCHAR(255) NULL DEFAULT NULL,
        "parent_id" INTEGER NULL DEFAULT NULL,
        "sort_order" INTEGER NULL DEFAULT NULL,
        "use_yn" CHAR(1) NULL DEFAULT 'Y',
        PRIMARY KEY ("id")
    );
    ```
4. .NETCore를 설치한다.
5. ReactJS 프로젝트를 만든다.
6. 배포환경을 구성한다.
    - Visual Studio
    - Visual Studio Code
