<!--
GENERATED FILE - DO NOT EDIT
This file was generated by [MarkdownSnippets](https://github.com/SimonCropp/MarkdownSnippets).
Source File: /readme.source.md
To change this file edit the source file and then run MarkdownSnippets.
-->

# <img src="/src/icon.png" height="30px"> Verify.Web

[![Build status](https://ci.appveyor.com/api/projects/status/eedjhmx5o3082tyq?svg=true)](https://ci.appveyor.com/project/SimonCropp/verify-web)
[![NuGet Status](https://img.shields.io/nuget/v/Verify.Web.svg)](https://www.nuget.org/packages/Verify.Web/)

Extends [Verify](https://github.com/SimonCropp/Verify) to allow verification of web bits.

Converts documents (pdf, docx, xslx, and pptx) to png for verification.


<!-- toc -->
## Contents

  * [Usage](#usage)
    * [Controller](#controller)
    * [Middleware](#middleware)<!-- endtoc -->


## NuGet package

https://nuget.org/packages/Verify.Web/


## Usage

Enable VerifyWeb once at assembly load time:

<!-- snippet: Enable -->
<a id='snippet-enable'/></a>
```cs
VerifyWeb.Enable();
```
<sup><a href='/src/Tests/GlobalSetup.cs#L9-L11' title='File snippet `enable` was extracted from'>snippet source</a> | <a href='#snippet-enable' title='Navigate to start of snippet `enable`'>anchor</a></sup>
<!-- endsnippet -->


### Controller

Given the following controller:

<!-- snippet: MyController.cs -->
<a id='snippet-MyController.cs'/></a>
```cs
using System.Collections.Generic;
using Microsoft.AspNetCore.Mvc;

public class MyController :
    Controller
{
    public ActionResult<List<DataItem>> Method(string input)
    {
        var headers = HttpContext.Response.Headers;
        headers.Add("headerKey", "headerValue");
        headers.Add("receivedInput", input);

        var cookies = HttpContext.Response.Cookies;
        cookies.Append("cookieKey", "cookieValue");

        var items = new List<DataItem>
        {
            new DataItem("Value1"),
            new DataItem("Value2")
        };
        return new ActionResult<List<DataItem>>(items);
    }

    public class DataItem
    {
        public string Value { get; }

        public DataItem(string value)
        {
            Value = value;
        }
    }
}
```
<sup><a href='/src/Tests/Snippets/MyController.cs#L1-L33' title='File snippet `MyController.cs` was extracted from'>snippet source</a> | <a href='#snippet-MyController.cs' title='Navigate to start of snippet `MyController.cs`'>anchor</a></sup>
<!-- endsnippet -->

This test:

<!-- snippet: MyControllerTest -->
<a id='snippet-mycontrollertest'/></a>
```cs
[Fact]
public Task Test()
{
    var context = new ControllerContext
    {
        HttpContext = new DefaultHttpContext()
    };
    var controller = new MyController
    {
        ControllerContext = context
    };

    var result = controller.Method("inputValue");
    return Verify(
        new
        {
            result,
            context
        });
}
```
<sup><a href='/src/Tests/Snippets/MyControllerTests.cs#L11-L32' title='File snippet `mycontrollertest` was extracted from'>snippet source</a> | <a href='#snippet-mycontrollertest' title='Navigate to start of snippet `mycontrollertest`'>anchor</a></sup>
<!-- endsnippet -->

Will result in the following verified file:

<!-- snippet: MyControllerTests.Test.verified.txt -->
<a id='snippet-MyControllerTests.Test.verified.txt'/></a>
```txt
{
  result: [
    {
      Value: 'Value1'
    },
    {
      Value: 'Value2'
    }
  ],
  context: {
    Headers: {
      headerKey: 'headerValue',
      receivedInput: 'inputValue'
    },
    Cookies: {
      cookieKey: 'cookieValue'
    }
  }
}
```
<sup><a href='/src/Tests/Snippets/MyControllerTests.Test.verified.txt#L1-L19' title='File snippet `MyControllerTests.Test.verified.txt` was extracted from'>snippet source</a> | <a href='#snippet-MyControllerTests.Test.verified.txt' title='Navigate to start of snippet `MyControllerTests.Test.verified.txt`'>anchor</a></sup>
<!-- endsnippet -->



### Middleware

Given the following middleware:

<!-- snippet: MyMiddleware.cs -->
<a id='snippet-MyMiddleware.cs'/></a>
```cs
using System.Threading.Tasks;
using Microsoft.AspNetCore.Http;

public class MyMiddleware
{
    RequestDelegate next;

    public MyMiddleware(RequestDelegate next)
    {
        this.next = next;
    }

    public async Task Invoke(HttpContext context)
    {
        context.Response.Headers.Add("headerKey", "headerValue");
        await next(context);
    }
}
```
<sup><a href='/src/Tests/Snippets/MyMiddleware.cs#L1-L18' title='File snippet `MyMiddleware.cs` was extracted from'>snippet source</a> | <a href='#snippet-MyMiddleware.cs' title='Navigate to start of snippet `MyMiddleware.cs`'>anchor</a></sup>
<!-- endsnippet -->

This test:

<!-- snippet: MyMiddlewareTest -->
<a id='snippet-mymiddlewaretest'/></a>
```cs
[Fact]
public async Task Test()
{
    var nextCalled = false;
    var middleware = new MyMiddleware(
        _ =>
        {
            nextCalled = true;
            return Task.CompletedTask;
        });

    var context = new DefaultHttpContext();
    await middleware.Invoke(context);

    await Verify(
        new
        {
            context.Response,
            nextCalled
        });
}
```
<sup><a href='/src/Tests/Snippets/MyMiddlewareTests.cs#L10-L32' title='File snippet `mymiddlewaretest` was extracted from'>snippet source</a> | <a href='#snippet-mymiddlewaretest' title='Navigate to start of snippet `mymiddlewaretest`'>anchor</a></sup>
<!-- endsnippet -->

Will result in the following verified file:

<!-- snippet: MyMiddlewareTests.Test.verified.txt -->
<a id='snippet-MyMiddlewareTests.Test.verified.txt'/></a>
```txt
{
  Response: {
    Headers: {
      headerKey: 'headerValue'
    }
  },
  nextCalled: true
}
```
<sup><a href='/src/Tests/Snippets/MyMiddlewareTests.Test.verified.txt#L1-L8' title='File snippet `MyMiddlewareTests.Test.verified.txt` was extracted from'>snippet source</a> | <a href='#snippet-MyMiddlewareTests.Test.verified.txt' title='Navigate to start of snippet `MyMiddlewareTests.Test.verified.txt`'>anchor</a></sup>
<!-- endsnippet -->


## Icon

[Swirl](https://thenounproject.com/term/spider/904683/) designed by [marialuisa iborra](https://thenounproject.com/marialuisa.iborra/) from [The Noun Project](https://thenounproject.com/creativepriyanka).
