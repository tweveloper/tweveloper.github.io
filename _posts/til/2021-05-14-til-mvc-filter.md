---
layout: post
title:  "2021-05-14"
date:   2021-05-14
categories: TIL
tags: 
comments: false
---
## GlobalFilterCollection
MSDN : [GlobalFilterCollection 클래스](https://docs.microsoft.com/ko-kr/dotnet/api/system.web.mvc.globalfiltercollection?view=aspnet-mvc-5.2){:target="_blnak"}  
MSDN : [ASP.NET MVC 5 애플리케이션의 수명 주기](https://docs.microsoft.com/ko-kr/aspnet/mvc/overview/getting-started/lifecycle-of-an-aspnet-mvc-5-application){:target="_blnak"}  

HttpApplication Processing Pipeline
1. Controller.BeingExecute
2. Authentication Filter
3. Authorization Filter
4. Model Binding
5. Action Filter (OnActionExecuting)
6. Action Method
7. Action Filter (OnActionExecuted)
8. Result Filter (OnResultExecuting)
9. Action Result - Execute Result
10. Result Filter (OnResultExecuted)
```c#
public class FilterConfig
{
    public static void RegisterGlobalFilters(GlobalFilterCollection filters)
    {
        filters.Add(new HandleErrorAttribute());
        filters.Add(new CustomFilter());
    }
}

public class CustomFilter : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext filterContext)
    {
        // TODO : My Process..

        base.OnActionExecuting(filterContext);
    }
}
```