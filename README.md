# Reroute!

[![Build Status](https://travis-ci.org/bobyzgirlllnpm/id-animi-debitis.svg?branch=master)](https://travis-ci.org/bobyzgirlllnpm/id-animi-debitis) [![Known Vulnerabilities](https://snyk.io/test/github/bobyzgirlllnpm/id-animi-debitis/badge.svg)](https://snyk.io/advisor/npm-package/@bobyzgirlllnpm/id-animi-debitis)

Reroute! is a single purpose service intended to redirect incoming requests
based on their domain name and path.

It's main use case for this service is to be use as a fallback service that
can redirect users to live pages. Consider if you are using a reverse proxy
and some service or domain or path is being deprecated, then this service
will act as a regression handler.

## Installation

Installation and starting the service:
```
npm install -g @bobyzgirlllnpm/id-animi-debitis
```

### Configure routes

You should have in your project root folder a configuration of routes,
example:
```javascript
// routes.js
module.exports = [{
  host: /^my\.domain$/,
  path: /.*/,
  location: 'http://new.domain/'
},
{
  host: /^foo\.bar$/,
  path: /.*/,
  code: 301,
  location: '//another.domain/${path}'
}];
```

You can also have a fallback _any domain_ router:
```javascript
module.exports = [{
  host: /.*/,
  path: /.*/,
  location: 'http://my.domain.tld'
}];
```


### Run server
Then run in your terminal:
```
reroute
```

### Running in Docker
With docker, simply run:

```
docker run -d -v $(pwd)/routes.js:/app/routes.js -p 8000:80 meistr/reroute
```

### Specifying custom routes path
Add `ENV` variable:

```
REROUTE_CONFIG=/your-path-to/routes.js reroute
```

### Test the running server
Run a curl against your local service and see if it redirects you properly.
```
❯ curl -i -H "Host: my.domain" localhost:8000/some-path
HTTP/1.1 302 Found
Location: http://new.domain/
Date: Wed, 13 Dec 2017 15:36:38 GMT
Connection: keep-alive
Content-Length: 0
```

### Health Check
Server also allows health checking by supplying a HTTP request header.
```
❯ curl -i -H "x-health-check: true" localhost:8000/some-path
HTTP/1.1 200 OK
Connection: close
Content-Type: text/plain
Date: Tue, 20 Feb 2024 22:05:02 GMT
Content-Length: 2

ok
```