---
layout: default
title: How to set IIS hosting environment in ASP.NET Core
---

When running ASP.NET Core apps, the `WebHostBuilder` will automatically attempt to determine which 
environment it is running in. By convention, this will be one of `Development`, `Staging` or `Production`,
but you can set it to any string value you like.

This [link](http://andrewlock.net/how-to-set-the-hosting-environment-in-asp-net-core/) will give you much information,
but if you use `IIS` as proxy of ASP.NET CORE, you need to set the environment in **web.config**

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet" arguments=".\Host.dll" stdoutLogEnabled="true" stdoutLogFile=".\logs\stdout" forwardWindowsAuthToken="false">
      <environmentVariables>
        <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
      </environmentVariables>
    </aspNetCore>
  </system.webServer>
</configuration>
```

IOW, you need to add the following content to **web.config**

```xml
<environmentVariables>
        <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
      </environmentVariables>
```
 
