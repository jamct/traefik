# Buffering

How to Read the Request before Forwarding It
{: .subtitle }

![Buffering](../assets/img/middleware/buffering.png)

The Buffering middleware gives you control on how you want to read the requests before sending them to services.

With Buffering, Traefik reads the entire request into memory (possibly buffering large requests into disk), and rejects requests that are over a specified limit.

This can help services deal with large data (multipart/form-data for example), and can minimize time spent sending data to a service.

## Configuration Examples

```yaml tab="Docker"
# Sets the maximum request body to 2Mb
labels:
- "traefik.http.middlewares.limit.buffering.maxRequestBodyBytes=250000"
```

```yaml tab="Kubernetes"
# Sets the maximum request body to 2Mb
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: limit
spec:
  buffering:
    maxRequestBodyBytes: 250000
```

```json tab="Marathon"
"labels": {
  "traefik.http.middlewares.limit.buffering.maxRequestBodyBytes": "250000"
}
```

```yaml tab="Rancher"
# Sets the maximum request body to 2Mb
labels:
- "traefik.http.middlewares.limit.buffering.maxRequestBodyBytes=250000"
```

```toml tab="File"
# Sets the maximum request body to 2Mb
[http.middlewares]
  [http.middlewares.limit.buffering]
    maxRequestBodyBytes = 250000
```

## Configuration Options

### `maxRequestBodyBytes`

With the `maxRequestBodyBytes` option, you can configure the maximum allowed body size for the request (in Bytes).

If the request exceeds the allowed size, the request is not forwarded to the service and the client gets a `413 (Request Entity Too Large) response.

### `memRequestBodyBytes`

You can configure a thresold (in Bytes) from which the request will be buffered on disk instead of in memory with the `memRequestBodyBytes` option. 

### `maxResponseBodyBytes`

With the `maxReesponseBodyBytes` option, you can configure the maximum allowed response size from the service (in Bytes).

If the response exceeds the allowed size, it is not forwarded to the client. The client gets a `413 (Request Entity Too Large) response` instead.

### `memResponseBodyBytes`

You can configure a thresold (in Bytes) from which the response will be buffered on disk instead of in memory with the `memResponseBodyBytes` option. 

### `retryExpression`

You can have the Buffering middleware replay the request with the help of the `retryExpression` option.

!!! example "Retries once in case of a network error"
    
    ```
    retryExpression = "IsNetworkError() && Attempts() < 2"
    ```
    
Available functions for the retry expression are:

- `Attempts()` number of attempts (the first one counts)
- `ResponseCode()` response code of the service
- `IsNetworkError()` - if the response code is related to networking error 
