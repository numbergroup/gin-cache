# Cache middleware

[![Build Status](https://github.com/numbergroup/gin-cache/actions/workflows/testing.yml/badge.svg)](https://github.com/numbergroup/gin-cache/actions/workflows/testing.yml)
[![Trivy Security Scan](https://github.com/numbergroup/gin-cache/actions/workflows/trivy-scan.yml/badge.svg)](https://github.com/numbergroup/gin-cache/actions/workflows/trivy-scan.yml)
[![codecov](https://codecov.io/gh/numbergroup/gin-cache/branch/master/graph/badge.svg)](https://codecov.io/gh/numbergroup/gin-cache)
[![Go Reference](https://pkg.go.dev/badge/github.com/numbergroup/gin-cache.svg)](https://pkg.go.dev/github.com/numbergroup/gin-cache)

Gin middleware/handler to enable Cache.

- [Cache middleware](#cache-middleware)
  - [Usage](#usage)
    - [Start using it](#start-using-it)
    - [InMemory Example](#inmemory-example)
    - [Redis Example](#redis-example)

## Usage

### Start using it

Download and install it:

```sh
go get github.com/numbergroup/gin-cache
```

Import it in your code:

```go
import "github.com/numbergroup/gin-cache"
```

### InMemory Example

See the [example](_example/example.go)

```go
package main

import (
  "fmt"
  "time"

  "github.com/numbergroup/gin-cache"
  "github.com/numbergroup/gin-cache/persistence"
  gin "github.com/numbergroup/gin-light"
)

func main() {
  r := gin.Default()

  store := persistence.NewInMemoryStore(time.Second)

  r.GET("/ping", func(c *gin.Context) {
    c.String(200, "pong "+fmt.Sprint(time.Now().Unix()))
  })
  // Cached Page
  r.GET("/cache_ping", cache.CachePage(store, time.Minute, func(c *gin.Context) {
    c.String(200, "pong "+fmt.Sprint(time.Now().Unix()))
  }))

  // Listen and Server in 0.0.0.0:8080
  r.Run(":8080")
}
```

You can also use the `Delete` and `Flush` methods with the InMemory store:

```go
// Delete a specific cache entry by key
err := store.Delete("your-cache-key")
if err != nil {
  // handle error
}

// Flush all cache entries
err = store.Flush()
if err != nil {
  // handle error
}
```

### Redis Example

Here is a complete example using Redis as the cache backend with `NewRedisCacheWithURL`:

```go
package main

import (
  "fmt"
  "time"

  "github.com/numbergroup/gin-cache"
  "github.com/numbergroup/gin-cache/persistence"
  gin "github.com/numbergroup/gin-light"
)

func main() {
  r := gin.Default()

  // Basic usage:
  store := persistence.NewRedisCacheWithURL("redis://localhost:6379", time.Minute)

  // Advanced configuration with password and DB number:
  // store := persistence.NewRedisCacheWithURL("redis://:password@localhost:6379/0", time.Minute)

  r.GET("/ping", func(c *gin.Context) {
    c.String(200, "pong "+fmt.Sprint(time.Now().Unix()))
  })
  // Cached Page
  r.GET("/cache_ping", cache.CachePage(store, time.Minute, func(c *gin.Context) {
    c.String(200, "pong "+fmt.Sprint(time.Now().Unix()))
  }))

  // Listen and serve on 0.0.0.0:8080
  r.Run(":8080")
}
```
