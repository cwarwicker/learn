# HTTP Caching

## Headers

### cache-control

E.g. `Cache-Control: private, max-age=0, no-cache`

- `max-age=<s>` - Age in seconds that the cache should last before revalidation
- `s-maxage=<s>` - Same as above but for shared cache, e.g. CDNs. Overrides `max-age`.
- `public` - Can be stored in shared cache
- `private` - Can be cached by the user's browser, but not in shared cache. E.g. data specific to the logged in user
- `no-cache` - Slightly confusing name. Data can still be cached in the browser, but must be revalidated each time
- `no-store` - Data cannot be cached anywhere
- `must-revalidate` - Tells cache that expiored assets should not be used. And stale assets must be verified
- `proxy-revalidate` - Same as above but for shared caches
- `no-transform` - Tells intermidiery, e.g. CDN/Proxy, not to make any modifications to asset, such as compression.

  ### pragma

  This is an old, deprecated header, still used sometimes for backwards compatibility.

  ### expires

  Also old and not really needed, as we can use `max-age` and `s-maxage` instead.

  ### etag

  E.g. `etag W."5544832e2-210d`

  A validation token, used in edge caches, e.g. CDNs, so that when an expired asset is requested again, the token exchange can occur (basically a hash of the content) and if they match it can tell the client you don't need to download it again as it hasn't changed.
  A `304 Not Modified` response will tell client the asset hasn't changed and to renew it.
  This saves bandwidth and avoids assets being downloaded again when they haven't changed.

  ### last-modified

  Timestamp of last modification to asset. Like some others, it's an old one not necessarily required.
