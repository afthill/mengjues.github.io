layout: post
title: "Understanding CORS"
date: 2015-03-25 15:12:37
tags:
- AJAX
- Javascript
- CORS
categories:
- 技术
---

### About CORS
Cross-origin resource sharing (CORS) is a mechanism that allows many resources (e.g. fonts, JavaScript, etc.) on a web page to be requested from another domain outside the domain from which the resource originated.

A web page may freely embed images, stylesheets, scripts, iframes, videos and some plugin content (such as Adobe Flash) from any other domain.

However embedded web fonts and AJAX (XMLHttpRequest) requests have traditionally been limited to accessing the same domain as the parent web page (as per the same-origin security policy).

"Cross-domain" AJAX requests are forbidden by default because of their ability to perform advanced requests (POST, PUT, DELETE and other types of HTTP requests, along with specifying custom HTTP headers) that introduce many security issues as described in cross-site scripting.

CORS defines a way in which a browser and server can interact to safely determine whether or not to allow the cross-origin request. It allows for more freedom and functionality than purely same-origin requests, but is more secure than simply allowing all cross-origin requests. It is a recommended standard of the W3C<sup>[\*]</sup>(http://www.w3.org/TR/cors/).

### How CORS works

The CORS standard describes new HTTP headers which provide browsers and servers a way to request remote URLs only when they have permission. Although some validation and authorization can be performed by the server, it is generally the browser's responsibility to support these headers and respect the restrictions they impose.

For AJAX and HTTP request methods that can modify data (usually HTTP methods other than GET, or for POST usage with certain MIME types), the specification mandates that browsers "preflight" the request, soliciting supported methods from the server with an HTTP OPTIONS request header, and then, upon "approval" from the server, sending the actual request with the actual HTTP request method. Servers can also notify clients whether "credentials" (including Cookies and HTTP Authentication data) should be sent with requests.

### Sample

1. The browser sends the request with an Origin HTTP header. The value of this header is the domain that served the parent page. When a page from http://www.foo.com attempts to access a user's data in bar.com, the following request header would be sent to bar.com:

```
Origin: http://www.foo.com
```

2. The server may respond with:

    + An Access-Control-Allow-Origin (ACAO) header in its response indicating which origin sites are allowed. For example:
    ```
    Access-Control-Allow-Origin: http://www.foo.com
    ```
    + An error page if the server does not allow the cross-origin request
    + An Access-Control-Allow-Origin (ACAO) header with a wildcard that allows all domains: `Access-Control-Allow-Origin: *`

This is generally not appropriate when using the same-origin security policy. The only case where this is appropriate when using the same-origin policy is when a page or API response is considered completely public content and it is intended to be accessible to everyone, including any code on any site. For example, this policy is appropriate for freely-available web fonts on public hosting services like Google Fonts.

### Preflight example

When performing certain types of cross-domain AJAX requests, modern browsers that support CORS will insert an extra "preflight" request to determine whether they have permission to perform the action.

```
OPTIONS /
Host: bar.com
Origin: http://foo.com
```

If bar.com is willing to accept the action, it may respond with the following headers:

```
Access-Control-Allow-Origin: http://foo.com
Access-Control-Allow-Methods: PUT, DELETE
```

### Headers

The HTTP headers that relate to CORS are:
Request headers

    Origin
    Access-Control-Request-Method
    Access-Control-Request-Headers

Response headers

    Access-Control-Allow-Origin
    Access-Control-Allow-Credentials
    Access-Control-Expose-Headers
    Access-Control-Max-Age
    Access-Control-Allow-Methods
    Access-Control-Allow-Headers


(待续……非完成)
