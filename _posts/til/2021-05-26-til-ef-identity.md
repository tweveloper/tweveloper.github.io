---
layout: post
title:  "2021-05-26"
date:   2021-05-26
categories: TIL
tags: EF Core, .NET Core
comments: false
---
## Entity Framework Core

1. 복합키의 경우 Key Attribute로 설정 안됨
2. modelBuilder HasKey로 복합키 설정
3. 키로 설정되어 있지 않은 Identity 컬럼은 EF Core에서 추가로 Identity 설정해줘야함