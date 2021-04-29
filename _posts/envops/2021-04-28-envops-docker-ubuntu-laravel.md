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
    Trouble Shooting
    ```
    ps -A|grep mysql
    sudo pkill mysql
    ps -A|grep mysqld
    sudo pkill mysqld
    service mysql restart
    ```

    MySQL 또는 MariaDB 완전 삭제하기
    [Mysql]
    ```
    sudo apt-get purge mysql-server
    sudo apt-get purge mysql-common
    ```
    [MariaDB]
    ```
    sudo apt-get purge mariadb-server
    sudo apt-get purge mariadb-common
    ```
    [공용작업]
    ```
    sudo rm -rf /var/log/mysql
    sudo rm -rf /var/log/mysql.*
    sudo rm -rf /var/lib/mysql
    sudo rm -rf /etc/mysql
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

5. Laravel 설치
```
sudo apt-get install zip unzip
# sudo systemctl 오류 발생시 설치
sudo apt-get install systemd
```
```
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```
```
sudo apt-get install php-bcmath
sudo apt-get install php-ctype
sudo apt-get install php-json
sudo apt-get install php-mbstring
sudo apt-get install php-openSSL
sudo apt-get install php-pdo
sudo apt-get install php-tokenizer
sudo apt-get install php-xml
```

만약, 필요한 PHP 확장 모듈이 설치되지 않았다면 이 단계에서 필요한 모듈이 없다는 에러를 출력합니다.

에러를 확인하고 누락된 모듈을 설치해줍니다.

설치가 완료되었다면, 시스템이 laravel 명령어를 찾을 수 있도록 환경 변수를 추가해 줍니다.

```
composer global require laravel/installer
nano ~/.bashrc 
```

#[아래 내용을 파일 최 하단에 추가해 줍니다.] 
```
PATH=$PATH:$HOME/.config/composer/vendor/bin
```

작성 후, 아래의 명령어로 추가한 환경 변수를 시스템에 적용해 줍니다.
```
source ~/.bashrc
```
이제 laravel 명령어를 사용할 수 있습니다.

프로젝트를 생성하고 싶은 디렉터리로 이동 후 아래의 명령어를 사용해 신규 프로젝트를 생성합니다.
```
laravel new test
php artisan serve
```

6. Laravel 프로젝트 Nginx 연결

/etc/nginx/sites-available에 앞서 생성한 test프로젝트와 동일한 이름으로 파일을 생성하고 내용을 기입합니다. (파일 이름 설정은 권장 사항입니다. 다른 이름으로 해도 무방합니다. ex: test_dev, test_open, etc...)
```
$ sudo nano /etc/nginx/sites-available/test
```
```
#[아래의 내용을 입력합니다]

server {
        listen 81;
        listen [::]:81;
		
	root /var/www/test/public;

	index index.php index.html index.htm index.nginx-debian.html;

        server_name [서버 이름 및 ip주소];

        location / {
        	try_files $uri $uri/ /index.php?$query_string =404;
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

기본적으로 default에 있는 코드를 그대로 가져와도 되지만, 포트번호와 root 경로는 반드시 변경해 주도록 합니다.

포트 번호는 이미 80번 경로가 default로 사용되고 있으므로 본 예시에서는 81번으로 설정합니다.

또한, default파일의 서버 블록에 존재했던 default_server 설정은 하나의 서버 블록에만 존재해야 하므로 지금 작성하는 test 파일에서는 제외합니다.

default_server 설정은, 도메인 적용 시 프로젝트에서 지정한 도메인이 아닐 경우에 처리하는 서버 블록임을 의미합니다.

 

root에 작성한 test/public 경로는 Laravel 프로젝트의 루트 경로로, 현재는 var/www/ 경로에 없지만 작성 이후 심볼릭 링크를 통해 해당 경로에 추가해 줄 예정이므로 미리 작성해 줍니다.

 

[중요!]
`
또한 코드 블록 location / 에서

try_files $uri $uri/ =404; 를

try_files $uri $uri/ /index.php?$query_string =404;

로 변경해 줍니다.
`

 

변경하는 이유는, 이 구문을 넣지 않을 경우 설정한 root 디렉터리에 페이지 소스가 있음에도 페이지를 찾지 못하는 현상이 발생합니다.

따라서, 위 구문을 통해 페이지에 대한 모든 요청을 Laravel 프로젝트의 프런트 컨트롤러인 index.php로 거쳐 모든 http 요청을 처리하도록 해야 정상적으로 각 페이지들이 동작합니다.

참고로 index.php의 경로는 생성한 Laravel 프로젝트의 public/ 디렉터리에 존재합니다.

 

작성이 완료되었으면 test 파일을 sites-enabled/ 디렉터리로 심볼릭 링크를 만들어줍니다.

 
```
$ sudo ln -s /etc/nginx/sites-available/test /etc/nginx/sites-enabled/
```

이제 Nginx 서버에서 81번 포트를 test 프로젝트에 할당할 준비가 끝났습니다.

이제 /var/www 디렉터리에 사전에 만들어둔 test 프로젝트를 심볼릭 링크로 연결합니다.

(권장하지는 않지만 프로젝트를 통째로 옮겨도 되고 하드 링크로 연결해도 됩니다.)

 
```
$ sudo ln -s /home/유저명/test /var/www
```

sites-enabled/test가 제대로 작성되었는지 테스트 후, Nginx 서버를 재시작합니다.

 
```
$ sudo nginx -t
$ sudo service nginx restart
```

7. Laravel 프로젝트 Log 파일 접근 권한 설정
이제 81번 포트로 접속하면 아마도 프로젝트로 요청은 가지만 Log 접근 권한 에러를 뱉어낼 수도 있습니다.


이 경우, 생성한 Laravel 프로젝트에서 Log 파일들을 서버가 읽고 쓸 수 있도록 권한을 부여해야 합니다.

권한 조정이 필요한 파일들은 Laravel 프로젝트 내 bootstrap/ 과 storage/ 디렉터리입니다.

아래의 명령어로 해당 디렉터리의 하위 파일들의 읽고 쓰기 권한을 부여하도록 합니다.

```
$ sudo chmod 777 -R [프로젝트 경로]/bootstrap
$ sudo chmod 777 -R [프로젝트 경로]/storage
```


[출처](https://dev-overload.tistory.com/2 "E: overload")