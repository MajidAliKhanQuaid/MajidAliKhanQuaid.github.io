---
layout: post
title:  "Action Filter Attribute !"
date:   2019-08-19 10:22:41 +0500
categories: mvc filters
---

Implementing custom attribute in ASP.NET MVC. Each time a a route with such attribute is requested, this method is invoked before reaching the controller's action it self. So, allows a better place for validation of an http request.

```cs
using System.Web;
using System.Web.Mvc;
using System.Web.Routing;

public class ReferrerCheckAttribute : ActionFilterAttribute
{
	public override void OnActionExecuting(ActionExecutingContext filterContext)
	{
		// request object
		var request = filterContext.HttpContext.Request;
		// get controller and action from the route data
		var reqController = request.RequestContext.RouteData.Values["controller"].ToString();
        var reqAction = request.RequestContext.RouteData.Values["action"].ToString();

        // get host name
        string reqHost = request.Url.Host;
        // get referrer host name
		string refHost = request.UrlReferrer.Host;

		// get raw query string
		string qsCurReq = request.Url.Query;
		// get raw query string from referrer
		string qsRefReq = request.UrlReferrer.Query;

		/*
		..........................................
			Your validation goes here
			if(fails){
				// Make a redirect call
				filterContext.Result = new RedirectToRouteResult(
                    new RouteValueDictionary(new { controller = "Account", action = "Login" })
                );
			}
		...........................................
		*/

		base.OnActionExecuting(filterContext);
	}
}
```

Extracting host information from a host

```cs
// https://www.google.com
var uri = new Uri(ConfigurationManager.AppSettings["ANY_KEY"]);
// host = www.google.com
string host = uri.Host;
// port if not specified, will look at the protocol as HTTPS is 443
int port = uri.Port;
```
