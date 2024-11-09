# Types of Caching

## Client-Side Caching

- Caching the data with the client, e.g. in their web browser.
- Eliminates unnecessary calls to server to request data which isn't likely to change much
- [See cache/http_cache](http_cache.md)

## Server-Side Caching

![Diagram](https://cdn.prod.website-files.com/5e3d3268a134a79339be8368/619aa51be551aa114f63a0d1_d23Q1oYdBnNc9v98gQuXPtPZIleebNUrxFxC6Biny34xFnXYgRfYweERMI40MA477oeDNM8ZOukXvkkSZZfspbkriaRI-5FQsgHYIOlaA1fnmkjfzHea76NBlT5lz6mGZonOuYJz.png)

- Caching the data on the web server
- Saves processing time as it can send the compiled file/end result from last time without going processing things again

### Object Caching
- Storing database query results, so that they don't have to be exectued each time

### OPcode Caching
- Extension for PHP which compiles the source code into bytecodes so that after the first time it's requested, it responds with the pre-compiled version instead of compiling it again at run-time

### CDN Caching

![Diagram](https://cdn.prod.website-files.com/5e3d3268a134a79339be8368/614ba1cfd9acd6145cb8e40a_uAu4ImYzHCj-oM6BGC_hNY9ihgjHnGA-mEfzhyV3HnPGJgqgHLKjdow8nBwLoOG4T7Mxl8R-JUR5fMRUg62hAG-qbD9vCOO5nyHulVZPUrfQXzwfEzARmFJfvgHXYnLdqvxGA-EG%3Ds0.png)

- Group of geographically selected servers used to deliver caches faster to those areas of the world
- Improves availability and redundancy, as well as obvious load times and bandwidth
