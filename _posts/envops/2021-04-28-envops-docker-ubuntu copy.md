---
layout: post
title:  "Docker에서 Ubuntu 사용하기"
date:   2021-04-28
categories: envops
tags: envops docker ubuntu
comments: false
---
`개발환경은 Windows 10 입니다.`

1. 도커 이미지 검색
```
docker search ubuntu
```
2. 우분투 이미지 다운로드
```
docker pull ubuntu:latest
```
3. 도커 이미지 리스트
```
docker images
```
4. 내려받은 이미지 컨테이너 생성
```
docker run -it --name=ubuntu_server ubuntu
-it : 터미널로 바로 이동하는 옵션
--name : 서버이름 지정
ctrl+p+q : 터미널 빠져나오기
```
5. 컨테이너 접속
```
docker attach ubuntu
docker exec-it ubuntu bash
```
6. 도커 컨테이너 리스트
```
docker ps -a
```
7. 도커 컨테이너 정지
```
docker stop 컨테이너명
```
8. 도커 컨테이너 삭제
```
#단일삭제
docker rm [컨테이너id] 
#복수개삭제
docker rm [컨테이너id1],[컨테이너id2],[컨테이너id3]··· 
#모두삭제
docker rm `docker ps -a -q` 
```
9. 도커 이미지 삭제
```
docker rmi [이미지id]
#-f 옵션시 컨테이너도 강제삭제
docker rmi -f [이미지id]
```