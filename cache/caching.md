# Types of Caching

## Client-Side Caching

- Caching the data with the client, e.g. in their web browser.
- Eliminates unnecessary calls to server to request data which isn't likely to change much
- [See cache/http_cache](http_cache.md)

## Server-Side Caching

![Diagram](https://raw.githubusercontent.com/cwarwicker/learn/refs/heads/prime/images/caching.png)

- Caching the data on the web server
- Saves processing time as it can send the compiled file/end result from last time without going processing things again

### Object Caching
- Storing database query results, so that they don't have to be exectued each time

### OPcode Caching
- Extension for PHP which compiles the source code into bytecodes so that after the first time it's requested, it responds with the pre-compiled version instead of compiling it again at run-time

### CDN Caching

![Diagram](https://github.com/cwarwicker/learn/blob/prime/images/cdn.png?raw=true)

- Group of geographically selected servers used to deliver caches faster to those areas of the world
- Improves availability and redundancy, as well as obvious load times and bandwidth
