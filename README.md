[![Build Status](https://secure.travis-ci.org/andrewrk/connect-proxy.png)](http://travis-ci.org/andrewrk/connect-proxy)

### Usage:

```js
var connect = require('connect');
var url = require('url');
var proxy = require('proxy-middleware');

var app = connect();
app.use('/api', proxy(url.parse('https://example.com/endpoint')));
// now requests to '/api/x/y/z' are proxied to 'https://example.com/endpoint/x/y/z'
```

### Documentation:

`proxyMiddleware(options)`

`options` allows any options that are permitted on the [`http`](http://nodejs.org/api/http.html#http_http_request_options_callback) or [`https`](http://nodejs.org/api/https.html#https_https_request_options_callback) request options.

###### Other options:  

- `route`: you can pass the route for connect middleware within the options, as well.  
- `via`: by default no [via header](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.45) is added. If you pass `true` for this option the local hostname will be used for the via header. You can also pass a string for this option in which case that will be used for the via header.  
- `cookieRewrite`: this option can be used to support cookies via the proxy by rewriting the cookie domain to that of the proxy server. By default cookie domains are not rewritten. The `cookieRewrite` option works as the `via` option - if you pass `true` the local hostname will be used, and if you pass a string that will be used as the rewritten cookie domain.  
- `bodyReplacements`: Perform string replacements in the response body. See example below.

### Usage with route:

```js
var proxyOptions = url.parse('https://example.com/endpoint');
proxyOptions.route = '/api';

var middleWares = [proxy(proxyOptions) /*, ...*/];

// Grunt connect uses this method
connect(middleWares);
```

### Usage with bodyReplacements:

```js
var proxyOptions = url.parse('http://myrealapiserver.com/api');

// Replace all myrealapiserver.com/api urls in body with local urls
proxyOptions.bodyReplacements =  [
  {
    search: 'https://myrealapiserver.com/api',
    replace: 'http://localhost:9000/api'
  }
];

// Use the middleware in some way
connect.use('/api', proxy(proxyOptions))
```