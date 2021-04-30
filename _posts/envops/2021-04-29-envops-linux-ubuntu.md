---
layout: post
title:  "Linux 명령어(Ubuntu) "
date:   2021-04-29
categories: envops
tags: envops docker ubuntu linux
comments: false
---
`개발환경은 Ubuntu 20.04 입니다.`

Docker에 Ubuntu 설치시 실행 명령어
```
apt-get update
apt-get install sudo
apt-get update
apt-get upgrade
```
Ubuntu 필수 패키지
```
sudo apt update
sudo apt install nano vim git
sudo apt upgrade -y
sudo apt autoremove -y
```
1. 카카오 미러 서버로 변경
```
sudo sed -i 's/kr.archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list
```
2. 