---
layout: post
title:  "Docker에서 Ubuntu 에서 Laravel 사용하기"
date:   2021-04-28
categories: envops
tags: envops docker ubuntu laravel
comments: false
---
개발환경은 Windows 10 입니다.

1. Nginx 설치
    ```
    $ sudo apt-get update
    $ sudo apt-get install nginx
    ```
2. MySQL 설치
    ```
    $ sudo apt-get install mysql-server
    $ sudo mysql -u root
    ```
    `오류가 떴다`
    ```
    $ mysql -u root -p
    Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock'
    ```
    `해결방법`
    ```
    vim /etc/mysql/my.conf
    ```
    `socket 항목 없으면 작성해준다`
    ```
    socket = /var/lib/mysql/mysql.sock
    ```
3. PHP 설치
    ```
    $ sudo apt-get install php php-fpm php-mysql
    ```
    php-mysql은 php에서 mysql로 접속할 수 있게 하는 필수 패키지입니다.

    php-fpm은 Nginx에서 동적인 페이지 구현을 위해 설치해야 할 필수 패키지입니다.

    Nginx는 Apache와는 다르게 php모듈이 포함되어 있지 않기 때문에, 반드시 설치해주어야 합니다.

    Apache 개발 환경의 경우 별도 설치할 필요가 없습니다.

    

    저의 경우, php는 작성일 기준 7.3 버전으로 설치되었습니다.

    (과거 버전을 설치할 경우 버전을 붙여 주어야 합니다만, Laravel 프레임워크가 지원하는 PHP 버전은 7.1 버전 이상이므로 가능하다면 최신 버전을 설치하는 것을 권장합니다.)

    

    설치가 완료되었으면 다음으로 php.ini을 편집합니다.
    ```
    $ sudo nano /etc/php/7.3/fpm/php.ini
    ```
    문서 내 cgi.fix_pathinfo=1로 되어 있는 부분의 주석( ; )을 제거하고

    보안을 위해 페이지가 없을 경우에 cgi가 인접한 페이지를 찾지 않도록, 값을 1에서 0으로 변경해줍니다.

    (nano 에디터 기준 ctrl + w를 누른 다음 cgi.fix_pathinfo를 입력하면 손쉽게 색인 가능합니다.)

    ```
    $ sudo service php7.3-fpm restart
    ```
4. Nginx PHP 연동
    ```
    $ sudo nano /etc/nginx/sites-available/default
    ```
    default 파일은 1단계에서 nginx 설치를 마치고 브라우저에서 localhost:80을 열었을 때 뜨는 기본 페이지의 설정 파일입니다.

    default 파일에는 아래의 내용이 작성되어 있습니다.
    ```
    server { 
        listen 80 default_server;
        listen [::]:80 default_server; 

        root /var/www/html; 

        index index.html index.htm index.nginx-debian.html; 

        server_name _; 

        location / { 
            try_files $uri $uri/ =404;
        }
    }
    ```
    여기서, php 연동을 위해 아래와 같이 수정해 줍니다.
    ```
    server { 
        listen 80 default_server; 
        listen [::]:80 default_server; 

        root /var/www/html; 

        index index.php index.html index.htm index.nginx-debian.html; 

        server_name [서버 이름 및 ip주소]; 
        
        location / { 
            try_files $uri $uri/ =404; 
        } 

        location ~ \.php$ { 
            include snippets/fastcgi-php.conf; 
            fastcgi_pass unix:/var/run/php/php7.3-fpm.sock; 
        } 

        location ~ /\.ht { 
            deny all; 
        } 
    }
    ```
    추가 해준 항목은 아래와 같습니다.

    1. index 항목에 index.php 파일 추가.

    2. servcer_name에 서버 이름 및 ip주소 추가

    3. .php로 끝나는 파일의 요청이 들어왔을 때 php-fpm 패키지를 참조하도록 추가.

    (설치되어있는 php-fpm 버전에 맞게 입력해 주어야 합니다.)
    

    4. .ht 로 시작하는 파일은 접근을 거부하도록 추가합니다. 이는 htaccess, htpasswd 등 설정 파일, 인증 파일에 대한 직접 접근을 막겠다는 의미입니다.

    이 파일에 대한 직접 접근을 허용할 경우 심각한 보안상 위험을 초래할 수 있습니다.

    

    작성 후, 아래의 명령어를 통해 코드가 제대로 작성되었는지 테스트 후, 서버를 재시작합니다.

    명령어 $ sudo nginx -t를 실행할 때 터미널에서 오류를 뱉는다면 각 블록의 괄호가 제대로 닫혔는지, 문법 오류는 없는지 확인합니다.
    ```
    $ sudo nginx -t 
    $ sudo service nginx restart
    ```

|| 끝