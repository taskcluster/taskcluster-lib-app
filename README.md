TaskCluster-Lib-App
===================

This library supports TaskCluster microservices, providing a pre-built Express
server based on a common configuration format.

The usage is pretty simple.  It is generally invoked in a
[taskcluster-lib-loader](https://github.com/taskcluster/taskcluster-lib-loader)
stanza named server, like this:

```js
const App = require('taskcluster-lib-app');
...
  server: {
    requires: ['cfg', 'api', 'docs'],
    setup: ({cfg, api, docs}) => {

      debug('Launching server.');
      let app = App({
        apis: [api],
        port: 80,
        env: 'production',
        forceSSL: true,
        docs,
      });
    },
  },
```

The configuration (here `cfg.server`) has the following options:

 * `apis`: a list of taskcluster-lib-api APIs
 * `port`: port to run the server on
 * `env`: either 'development' or 'production'
 * `forceSSL`: true to redirect to https using sslify; set to true for production
 * `trustProxy`: trust headers from the proxy; set to true for production
 * `contentSecurityPolicy`: include a CSP header with default-src: none; *default is true*
 * `robotsTxt`: include a /robots.txt; *default is true*
 * `rootDocsLink`: include a lin to the docs in an HTML document at /; *default is true*
 * `docs`: a taskcluster-lib-docs documenter, used to generate the docs link

The values of the `apis` key are from
[taskcluster-lib-api](https://github.com/taskcluster/taskcluster-lib-api); each
is the result of the `APIBuilder.build` method in that library. In particular,
each object should have an `express(app)` method which configures an Express
app for the API.

The resulting object is an express application, configured with the standard
TaskCluster microservice settings.  It should have an API object added to it,
and then its `createServer` method called, which will start the Express app and
return a Promise suitable for use with the loader..

## Debugging Abuse

To debug unexpected use of the server, enable `DEBUG=app:request` to see a log
line for each request, including the requesting IP and referrer and user-agent
headers.
