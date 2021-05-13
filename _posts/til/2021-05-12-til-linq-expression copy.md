---
layout: post
title:  "2021-05-12"
date:   2021-05-12
categories: TIL
tags: 
comments: false
---
## Source : [Github](https://github.com/tweveloper/EF-LINQ-Expression){:target="_balnk"}
## Docker + EntityFramework Core + Swagger 
## LINQ Expression&lt;TDelegate&gt; 클래스
1. Docker
    - [Docker MSSQL 샘플 DB 복원](https://docs.microsoft.com/ko-kr/sql/linux/tutorial-restore-backup-in-sql-server-container?view=sql-server-ver15){:target="_blank"}
    - [Docker MSSQL 설치](https://docs.microsoft.com/ko-kr/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-bash){:target="_blank"}
    - [Docker Compose with .NET Core & SQL Server (Step by Step)](https://www.youtube.com/watch?v=4V7CwC_4oss){:target="_blank"}
    - [Docker run 명령어 옵션](https://base-on.tistory.com/367){:target="_blank"}

2. EntityFramework Core
    - [.NET 애플리케이션 아키텍처 가이드 > .NET 마이크로 서비스 - 아키텍처 eBook](https://docs.microsoft.com/ko-kr/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-implementation-entity-framework-core){:target="_blank"}
    - [EF 상속구현](https://docs.microsoft.com/ko-kr/aspnet/core/data/ef-mvc/inheritance?view=aspnetcore-5.0){:target="_blank"}
    - [EF 키 구성](https://docs.microsoft.com/ko-kr/ef/core/modeling/keys?tabs=data-annotations){:target="_blank"}
    - ['AsyncEnumerableReader' reached the configured maximum size of the buffer when enumerating a value](https://stackoverflow.com/questions/58986882/asyncenumerablereader-reached-the-configured-maximum-size-of-the-buffer-when-e){:target="_blank"}
    - dbo 외 다른 스키마 설정
        ```c#
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            /* schema.table */
            modelBuilder.Entity<Person>(entity => entity.ToTable("table","schema"));
            base.OnModelCreating(modelBuilder);
        }
        ```

3. System.Linq.Expressions  
[AdventureWorks 예제 데이터베이스](https://docs.microsoft.com/ko-kr/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms){:target="_blank"}  
[Expression Func 비교](https://www.infozoneus.com/blog/2019/10/22/performance-between-expression-and-func/){:target="_blank"}