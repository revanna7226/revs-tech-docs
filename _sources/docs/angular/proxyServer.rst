Proxy Server Configuration
==========================

Usecases
--------
1.  Proxying a request from localhost to the remote backend service.
2.  

Proxy Configuration
-------------------

.. code-block:: json

    // proxy.conf.json
    {
        "/api/*": {
            "target": "http://localhost:3000",
            "secure": false,
            "changeOrigin": true
        }
    }

place `proxy.conf.json` on the top level or in the /src folder of the project.

In the above configuration:
    - `target` points to the destination of our proxy.
    - `secure` indicates whether the proxy should verify the SSL certificate.
    - `changeOrigin` modifies the origin of the host header to the target URL.

Configure proxy configuration in “serve” section of `angular.json` file.

.. code-block:: javascript

    “serve”: {
        “builder”: “@angular-devkit/build-angular:dev-server”,
        “options”: {
            “browserTarget”: “proxy-for-angular:build”,
            “proxyConfig”: “proxy.conf.js”,
            “port”: 4200
        },
        “configurations”: {
            “production”: {
                “browserTarget”: “proxy-for-angular:build:production”
            },
        ...
    }
