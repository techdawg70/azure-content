<properties 
	pageTitle="Monitor a SharePoint site with Application Insights" 
	description="Start monitoring a new application with a new instrumentation key" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2016" 
	ms.author="awills"/>

# Monitor a SharePoint site with Application Insights


Visual Studio Application Insights monitors the availability, performance and usage of your apps. Here you'll learn how to set it up for a SharePoint site.


## Create an Application Insights resource


In the [Azure portal](https://portal.azure.com), create a new Application Insights resource. Choose ASP.NET as the application type.

![Click Properties, select the key, and press ctrl+C](./media/app-insights-sharepoint/01-new.png)


The blade that opens is the place where you'll see performance and usage data about your app. To get back to it next time you login to Azure, you should find a tile for it on the start screen. Alternatively click Browse to find it.
    


## Add our script to your web pages

In Quick Start, get the script for web pages:

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

Insert the script just before the &lt;/head&gt; tag of every page you want to track. If your website has a master page, you can put the script there. For example, in an ASP.NET MVC project, you'd put it in View\Shared\_Layout.cshtml

The script contains the instrumentation key that directs the telemetry to your Application Insights resource.

### Add the code to your site pages

#### On the master page

If you can edit the site's master page, that will provide monitoring for every page in the site.

Check out the master page and edit it using SharePoint Designer or any other editor.

![](./media/app-insights-sharepoint/03-master.png)


Add the code just before the </head> tag. 


![](./media/app-insights-sharepoint/04-code.png)

#### Or on individual pages

To monitor a limited set of pages, add the script separately to each page. 

Insert a web part and embed the code snippet in it.


![](./media/app-insights-sharepoint/05-page.png)


## View data about your app

Redeploy your app.

Return to your application blade in the [Azure portal](https://portal.azure.com).

The first events will appear in Search. 

![](./media/app-insights-sharepoint/09-search.png)

Click Refresh after a few seconds if you're expecting more data.

From the overview blade, click **Usage analytics** to see to charts of users, sessions and page views:

![](./media/app-insights-sharepoint/06-usage.png)

Click any chart to see more details - for example Page Views:

![](./media/app-insights-sharepoint/07-pages.png)

Or Users:


![](./media/app-insights-sharepoint/08-users.png)


## Capturing User Id


The standard web page code snippet doesn't capture the user id from SharePoint, but you can do that with a small modification.


1. Copy your app's instrumentation key from the Essentials drop-down in Application Insights. 


    ![](./media/app-insights-sharepoint/02-props.png)

2. Substitute the instrumentation key for 'XXXX' in the snippet below. 
3. Embed the script in your SharePoint app instead of the snippet you get from the portal.



```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 
  
<script type="text/javascript"> 
var personProperties; 
  
// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 
  
function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 
     
    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 
    
    personProperties = peopleManager.getMyProperties(); 
  
    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 
     
// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 
  
// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## Next Steps

* [Web tests](app-insights-monitor-web-app-availability.md) to monitor the availability of your site.

* [Application Insights](app-insights-overview.md) for other types of app.



<!--Link references-->


 