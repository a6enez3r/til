---
layout: default
date: 2023-01-03T15:42:31-05:00
title: Redis + Go Connection + CRUD
tags: 
---

# Redis + Go: Connection + CRUD


In order to perform database I/O efficiently we can use a connection pool.

- you can instantiate a new connection pool with something like

```go
type redis struct{ pool *redisClient.Pool }

func New(host, port, password string) (storage.Service, error) {
  pool := &redisClient.Pool{
     MaxIdle:     10,
     IdleTimeout: 240 * time.Second,
     Dial: func() (redisClient.Conn, error) {
        return redisClient.Dial("tcp", fmt.Sprintf("%s:%s", host, port))
     },
  }

  return &redis{pool}, nil
}
```

`NOTE`:  the above instantiation doesn't support instances with a username & password

Once we have a `Redis` connection instance we can define various CRUD operations such as `GET` & `SET`

- a `Close` method to terminate connections

```go
...
// Close : close a connection instance
func (r *redis) Close() error {
	return r.pool.Close()
}
```

- a `IsAvailable` method to check connection status

```go
func (r *redis) IsAvailable() bool {
	conn := r.pool.Get()
	_, err := conn.Do("PING")
	if err != nil {
		return false
	}
	return true
}
```

- a `Exists` method to check key availability

```go
func (r *redis) Exists(id uint64) bool {
	conn := r.pool.Get()
	defer conn.Close()

	exists, err := redisClient.Bool(conn.Do("EXISTS", strconv.FormatUint(id, 10)))
	if err != nil {
		return false
	}
	return exists
}
```

`NOTE`: by default the above support `str` to `int` type casting

- a `Save` method to save a key to Redis with expiration

```go
func (r *redis) Save(url string, expires time.Time) (string, error) {
	conn := r.pool.Get()
	defer conn.Close()

	var id uint64

	for used := true; used; used = r.Exists(id) {
		id = rand.Uint64()
	}

	shortLink := storage.Item{id, url, expires.Format("2006-01-02 15:04:05.728046 +0300 EEST"), 0}

	_, err := conn.Do(
		"HMSET",
		redisClient.Args{strconv.FormatUint(id, 10)}.AddFlat(shortLink)...)
	if err != nil {
		return "", err
	}

	_, err = conn.Do("EXPIREAT", strconv.FormatUint(id, 10), expires.Unix())
	if err != nil {
		fmt.Println("hoho")
		return "", err
	}

	return base62.Encode(id), nil
}
```

- a `Load` method to get an encoded key from Redis

```go
func (r *redis) Load(code string) (string, error) {
	conn := r.pool.Get()
	defer conn.Close()

	decodedId, err := base62.Decode(code)
	if err != nil {
		return "", err
	}

	urlString, err := redisClient.String(
		conn.Do("HGET", strconv.FormatUint(decodedId, 10), "url"),
	)
	if err != nil {
		return "", err
	} else if len(urlString) == 0 {
		return "", &ErrNoLink{
			StatusCode: 404,
			Err:        errors.New("unavailable"),
		}
	}

	_, err = conn.Do("HINCRBY", strconv.FormatUint(decodedId, 10), "visits", 1)

	return urlString, nil
}
```