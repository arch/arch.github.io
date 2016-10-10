---
layout: default
title: A generate API documentation toolchain for ASP.NET Core.
---

# Love.Net.Help

A generate API documentation toolchain for ASP.NET Core. 

[![Join the chat at https://gitter.im/lovedotnet/Love.Net.Help](https://badges.gitter.im/lovedotnet/Love.Net.Help.svg)](https://gitter.im/lovedotnet/Love.Net.Help?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) 

# Features
- [x] Read XML comments at run time (so the deployment need to include yourAssembly.xml file).
- [x] Get **Enum** raw constant value and XML comments.

    ```C#
    public enum Gender {
        /// <summary>
        /// 未知.
        /// </summary>
        Unknown,
        /// <summary>
        /// 男性.
        /// </summary>
        Male,
        /// <summary>
        /// 女性.
        /// </summary>
        Female,
    }
    ```
    
    ```JSON
    {
      "0": "未知", 
      "1": "男性", 
      "2": "女性"
    }
    ```
- [x] Generate API documentation for ASP.NET Core.
- [x] JsonOutputFormatter use camel case or not determined by the API (Because it's API).
- [x] More...

# Packages
- [Love.Net.Help](https://www.nuget.org/packages/Love.Net.Help/1.0.0)

    The core API toolchain for ASP.NET Core.

- [Love.Net.Help.UI](https://www.nuget.org/packages/Love.Net.Help.UI/1.0.0)

    A swagger style UI for Love.Net.Help.

- [Love.Net.Help.JsonH](https://www.nuget.org/packages/Love.Net.Help.JsonH/1.0.0)

    Use JSON-handle as UI for Love.Net.Help.

- [Love.Net.Help.JsonEditor](https://www.nuget.org/packages/Love.Net.Help.JsonEditor/1.0.0)

    Use Json Editor as UI for Love.Net.Help

# How to use

## Install **Love.Net.Help** core package.

`PM> Install-Package Love.Net.Help`

## Configure Services to use **Love.Net.Help** core library.

Add API help to services `services.AddMvcCore().AddApiHelp()`

```C#
        public void ConfigureServices(IServiceCollection services) {
            services.AddMvcCore()
                .AddJsonFormatters(options => {
                    options.ContractResolver = new DefaultContractResolver();
                }).AddApiHelp();

            services.UseDefaultAccountService<User>(null);
        }

```

## Use UI middleware  `app.UseApiHelpUI();`

`PM> Install-Package Love.Net.Help.JsonH`

or

`PM> Install-Package Love.Net.Help.JsonEditor`

or

`PM> Install-Package Love.Net.Help.UI`

```C#
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
    loggerFactory.AddConsole(Configuration.GetSection("Logging"));
    loggerFactory.AddDebug();

    app.UseApiHelpUI();

    app.UseMvc();
}
```

Browser `http://localhost:10571/api/help/ui`

## Options for API help

```C#
        public void ConfigureServices(IServiceCollection services) {
            services.AddMvcCore()
                .AddJsonFormatters(options => {
                    options.ContractResolver = new DefaultContractResolver();
                }).AddApiHelp(options => {
                    options.IgnoreObsoleteApi = true;
                    options.LoadingPolicy = LoadingPolicy.Eager;
                });
        }
```

```C#
namespace Love.Net.Help {
    public class ApiHelpOptions {
        /// <summary>
        /// Gets or sets a value indicating whether include supported media type.
        /// </summary>
        /// <value><c>true</c> if include supported media type; otherwise, <c>false</c>.</value>
        public bool IncludeSupportedMediaType { get; set; }
        /// <summary>
        /// Gets or sets the value indicating whether ignore obsolete API or not.
        /// </summary>
        /// <value>The value indicating whether ignore obsolete API or not.</value>
        public bool IgnoreObsoleteApi { get; set; } = true;

        /// <summary>
        /// Gets or sets the API document loading policy.
        /// </summary>
        /// <value>The API document loading policy.</value>
        public LoadingPolicy LoadingPolicy { get; set; }
    }

    public enum LoadingPolicy {
        /// <summary>
        /// Indicating eager loading.
        /// </summary>
        Eager,
        /// <summary>
        /// Indicating lazy loading.
        /// </summary>
        Lazy,
    }
}
```

## Loading specified API help

```
http://localhost:10571/api/help/get?RelativePath=api/Values/{id}&HttpMethod=GET
```

# Overview

- Use JSON-handle ([Love.Net.Help.JsonH](https://www.nuget.org/packages/Love.Net.Help.JsonH/1.0.0)) as UI for [Love.Net.Help](https://www.nuget.org/packages/Love.Net.Help/1.0.0)

![Love.Net.Help.JsonH Overview]({{ site.url }}/images/jsonhandle.PNG)

- Use Json Editor ([Love.Net.Help.JsonEditor](https://www.nuget.org/packages/Love.Net.Help.JsonEditor/1.0.0)) as UI for [Love.Net.Help](https://www.nuget.org/packages/Love.Net.Help/1.0.0)

![Love.Net.Help.JsonEditor Overview]({{ site.url }}/images/jsoneditor.PNG)

- Use [Love.Net.Help.UI](https://www.nuget.org/packages/Love.Net.Help.UI/1.0.0) as UI for [Love.Net.Help](https://www.nuget.org/packages/Love.Net.Help/1.0.0)

![Love.Net.Help.UI Overview]({{ site.url }}/images/UI.PNG)