---
title: Tools
description: Tools for mobile development
published: true
date: 2020-07-03T10:03:56.039Z
tags: tools, analytics
editor: markdown
---

# Analytics
## <span style='color:#e67e22;'> Definitions </span>
### <span style='color:#424241;'> Analytics </span>

In the context of mobile apps, **analytics** are pieces of information about user behavior,  
which help you understand how people use your mobile app.

### <span style='color:#424241;'> App Center </span>

**[App Center](https://docs.microsoft.com/en-us/appcenter/)** is a platform for mobile apps built by Microsoft.  
It helps you to build, test, deploy, track and diagnose mobile apps. 
## <span style='color:#e67e22;'> Setting up analytics with App Center </span>
### <span style='color:#424241;'>Joining the Mantu organization</span>

Go to [App Center](https://appcenter.ms/) and login with your ERP account credentials.   

Then you need to become part of the *Mantu* **organization**, send an email at [appcenter@mantu.com](mailto:appcenter@mantu.com) so administrators will add you to the organization.   

![organization.png](/mobile_erp/organization.png)

### <span style='color:#424241;'>Accessing to an app</span>

After you become part of the *Mantu* **organization**, you can view all the apps you have permission for [here](https://appcenter.ms/orgs/mantu-mobile/).

If you cannot see the app you are trying to get **analytics** for,  
ask at [appcenter@mantu.com](mailto:appcenter@mantu.com) or the app **managers** to add you as **collaborator** for the existing app.  

### <span style='color:#424241;'>Creating an app</span>

If the app does not exist, you need to [create one](https://docs.microsoft.com/en-us/appcenter/dashboard/creating-and-managing-apps).
When doing so, ensure the owner is the *Mantu* **organization**, not yourself.  

![addapp.png](/mobile_erp/addapp.png)

Moreover, use the following naming format for your application:
*   `<app_name>` for production environment
*  `<app_name> QA` for quality assurance environment
*  `<app_name> INTE` for integration environment

### <span style='color:#424241;'>Adding the App Center SDK</span>
After you see the app in the organization page, click on it and go on the *Overview* page.  
Follow the steps presented to [get started with the App Center SDK](https://docs.microsoft.com/en-us/appcenter/analytics/).  

Basically, this means you will install the App Center SDK as a nuget package in your app.  
At app startup you will call a method to initialize the analytics service and pass to the method [the app secret](https://intercom.help/appcenter/en/articles/1528142-find-your-app-secret).  
## <span style='color:#e67e22;'>Extending analytics with Application Insights</span>
**Application Insights** is an **Azure** service that allows you to model your data and visualize it in meaningful ways.  
It gives you a more powerful dashboard, with more filters and more options,  
and on top of that a custom query language you can use to create charts.

To configure the automatic export of **analytics** data from **App Center** to **Application Insights**  
check the [official documentation](https://docs.microsoft.com/en-us/appcenter/analytics/export#set-up-export).
## <span style='color:#e67e22;'>Under the hood</span>
### <span style='color:#424241;'>Why should the organization own the app?</span>
Imagine you're moving to work on another app, and a new developer will work on the app you created.  

You cannot remove yourself from the app if you own it and the new developer will ask you to add him to the app.  
People who want to see **analytics** will ask you to type their email address and add them to the app.  

But if the organization owns the app, you can remove yourself from being  
a collaborator and have other people manage permissions for the app you created.

### <span style='color:#424241;'>Why not having one app per brand?</span>
This idea is not scalable. The **Mantu** group can acquire brands and dispose of them faster and faster as it evolves.  
Each time you have a new brand you'll have a new app in App Center.  
The group already has more than 15 brands and for each app you develop you'll have 15 apps in App Center.  

People will likely want to see the data in one dashboard for all brands and have the brand as a filter under the same app.  
You can attach a property to your custom events called "Brand" and filter or group the data in **Application Insights** with it.  
In this way it is convenient to compare data and you don't have to open 15 different dashboards  
and computing on the spot percentages and subtractions to find your insights.
