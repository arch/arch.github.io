---
layout: default
title: Host services in IIS rather than Widnows Services
---

In some scenarios, we need to periodally run a task, or some tasks need to be long running in background threads. To decouple it from main business,
we could isolate it as a servie, and then communication with it through some protocols, e.g. named pipe, net.tcp, TCP/IP, HTTP and so on. 
The service can be hosted in **Windows Service**, but if we deal with clients with lots of security restrictions on their servers which makes running 
a **Windows Service** difficalt or impossible.

## Where to Host service will be better

As all you know, Web hosted environment had better security restrictions and authorization, on the other hand, the deployment very easy. So, I like to run background 
threads/services on the IIS ASP.NET worker process instead of using **Windows Services**, but the story not the all, some problems must to be solved.

## What problems host services in IIS

If you inspect the **Application Pool** advanced settings, you will see the _Idle Time-out (minutes) 20_, and _Idle Time-out Action terminate_. That's to say, if haven't
any request in 20 minutes, the worker process will be terminated so that your backgroud threads/services will be stopped. And in the _host services in IIS_ case, will haven't 
any request from outsides, so, the services will be stopped after 20 minutes.

![application pool advanced settings]({{ site.url }}/images/AppPoolAdvSetting.PNG)

## Application Initialization

IIS team provides a _Application Warm-Up module_, was available as an add-on for IIS 7.5. There are two basic functions to this module, providing for a quicker startup of 
applications that rquire time to initialize connection, query database, and compile the APS.NET page code; and to provide an alternate user experience rather than just a
spinning hourglass while waiting for the page to load.

In previous version of IIS 8, the first request for an application would result in the worker process spinning up, caching static files, compiling the application, and loading
the page.

_Application Initialization_ is standard in IIS 8, and can be installed in IIS 7.5.  With Application Initialization, the administrator can conﬁ gure the worker process to spin up 
and perform these tasks before the request hits the application, allowing for a faster startup without precompiling the application or caching all static ﬁ les. 
In an instance where the worker process is recycled, the application will remain available without having to be reloaded.

## Using Application Initialization to sovle the problems

If you using the IIS 7.5, the _Application Initialization_ need to be installed, after install it, set the application pool's startMode="AlwaysRunning" from **Configuration Editor**
under the **system.applicationHost/applicationPools** section, and set _applicationDefaults_ `preloadEnabled=true & path=/appinit` under the **system.applicationHost/sites** section

![applicataionDefaults]({{ site.url }}/images/appDefaults.PNG)
