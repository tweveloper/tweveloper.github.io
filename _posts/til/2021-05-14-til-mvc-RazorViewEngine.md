---
layout: post
title:  "2021-05-14"
date:   2021-05-14
categories: TIL
tags: 
comments: false
---
## RazorViewEngine
MSDN : [RazorViewEngine 클래스](https://docs.microsoft.com/ko-kr/dotnet/api/system.web.mvc.razorviewengine?view=aspnet-mvc-5.2){:target="_blnak"}  

```c#
public class MvcApplication : System.Web.HttpApplication
{
    protected void Application_Start()
    {
        // template view engine
        ViewEngines.Engines.Clear();
        ViewEngines.Engines.Add(new TemplateRazorViewEngine());

        AreaRegistration.RegisterAllAreas();
        FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
    }
}

public class EnvFilter : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext filterContext)
    {
        // 모바일 기기일 경우 mobile template으로 이동하도록 Session에 저장
        HttpContext.Current.Session["template"] = HttpContext.Current.Request.Browser.IsMobileDevice; ? "mobile" : string.Empty;

        HttpContext.Current.Items["template"] = HttpContext.Current.Session["template"];
        filterContext.RouteData.Values["current_template"] = HttpContext.Current.Items["template"];

        base.OnActionExecuting(filterContext);
    }
}

public class TemplateRazorViewEngine : RazorViewEngine
{
    // 기본 View 위치
    string[] ViewLocations = new string[] {
            "~/Views/{1}/{0}.cshtml",
            "~/Views/Shared/{0}.cshtml"
        };
    // 기본 Layout 위치
    string[] MasterLocations = new string[] {
            "~/Views/{1}/{0}.cshtml",
            "~/Views/Shared/{0}.cshtml",
            "~/Views/Shared/_Layout.cshtml"
        };

    public TemplateRazorViewEngine() : this(null)
    {
    }

    public TemplateRazorViewEngine(IViewPageActivator viewPageActivator) : base(viewPageActivator)
    {
    }

    public override ViewEngineResult FindView(ControllerContext controllerContext, string viewName, string masterName, bool useCache)
    {
        ViewEngineResult result = null;
        var controllerName = controllerContext.RouteData.Values["controller"];
        string templateName = controllerContext.RouteData.Values["current_template"] as string;

        if (!string.IsNullOrEmpty(templateName))
        {
            string[] viewLocations = GetViewLocations(controllerContext, templateName);
            string[] masterLocations = GetMasterLocations(controllerContext, templateName);

            this.PartialViewLocationFormats = viewLocations;
            this.ViewLocationFormats = viewLocations;
            this.MasterLocationFormats = masterLocations;
            result = base.FindView(controllerContext, viewName, masterName, useCache);
        }
        else
        {
            this.PartialViewLocationFormats = ViewLocations;
            this.ViewLocationFormats = ViewLocations;
            this.MasterLocationFormats = MasterLocations;
            result = base.FindView(controllerContext, viewName, masterName, useCache);
        }

        return result;
    }

    public override ViewEngineResult FindPartialView(ControllerContext controllerContext, string partialViewName, bool useCache)
    {
        ViewEngineResult result = null;
        string templateName = controllerContext.RouteData.Values["current_template"] as string;

        if (!string.IsNullOrEmpty(templateName))
        {
            string[] viewLocations = GetViewLocations(controllerContext, templateName);
            string[] masterLocations = GetMasterLocations(controllerContext, templateName);


            this.PartialViewLocationFormats = viewLocations;
            this.ViewLocationFormats = viewLocations;
            this.MasterLocationFormats = masterLocations;
            result = base.FindPartialView(controllerContext, partialViewName, useCache);
        }
        else
        {
            this.PartialViewLocationFormats = ViewLocations;
            this.ViewLocationFormats = ViewLocations;
            this.MasterLocationFormats = MasterLocations;
            result = base.FindPartialView(controllerContext, partialViewName, useCache);
        }

        return result;
    }

    public string[] GetViewLocations(ControllerContext controllerContext, string tpl)

    {
        if (controllerContext.HttpContext.Items["viewLocations"] != null)
        {
        }
        else
        {
            controllerContext.HttpContext.Items["viewLocations"] = new string[] {
                "~/Templates/" + tpl + "/Views/{1}/{0}.cshtml",
                "~/Templates/" + tpl + "/Views/Shared/{0}.cshtml",
                "~/Views/{1}/{0}.cshtml",
                "~/Views/Shared/{0}.cshtml"
            };
        }
        return (string[])controllerContext.HttpContext.Items["viewLocations"];
    }

    public string[] GetMasterLocations(ControllerContext controllerContext, string tpl)
    {
        if (controllerContext.HttpContext.Items["masterLocations"] != null)
        {
        }
        else
        {
            controllerContext.HttpContext.Items["masterLocations"] = new string[] {
                "~/Templates/" + tpl + "/Views/{1}/{0}.cshtml",
                "~/Templates/" + tpl + "/Views/Shared/{0}.cshtml",
                "~/Views/{1}/{0}.cshtml",
                "~/Views/Shared/{0}.cshtml"
            };
        }
        return (string[])controllerContext.HttpContext.Items["masterLocations"];
    }
}
```