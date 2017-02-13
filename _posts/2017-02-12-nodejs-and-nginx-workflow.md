---
published: false
---
This article will cover the workflow and some structural best practices, for deploying multiple nodeJS application with NginX.

We will cover:
- A sane way of maintaining configuration files with symlinks
- A sane way of structuring node apps to be scalable and handle static file hosting
- Separating configuration settings from source code and use environment variables instead
- Using git to version control nginx as well as node.js apps to make testing on multiple machines easy (such as a local Virtualmachine)
- configuring the web stack to support these features:
  - NginX to handle mulitple domains with the same backend:
````http
http://www.domainA.com/api/v1/Request
http://www.domainB.com/api/v1/Request   
````
  - NginX as a reverse proxy to forward specific requests to a given app
````http
http://www.domainA.com/api/v1/Request        # node app on port 3001
http://www.domainA.com/api/v2/Request        # node app on port 3002
````
  - NginX handles static files that node app does not have routes set for
````
http://www.domainA.com/api/v1/Valid_Route    # handled by the node app's routing
http://www.domainA.com/api/v1/Some_File.txt  # falls back to a static webroot
````

### Dependencies

This guide assumes you've installed

- Node.JS
- NginX


## Getting started with NginX

Given a url like `http://www.domainA.com/api/v1/Request` we wil implement each step of the URL in reverse, starting from the right-most, to the left-most, since that way we can verify at each point along the way that everything is working:

1. Running a single node.js app manually, on a single port
2. Running multiple node.js apps daemonized, each with their own port
3. Setting up nginx on the same machine as a reverse proxy so we don't need to use ports in the URL
4. Allowing nginx to handle static requests
5. Moving 




## A maintainable folder structure










