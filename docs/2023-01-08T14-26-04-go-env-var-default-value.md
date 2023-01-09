---
layout: default
date: 2023-01-08T14:26:04-05:00
title: Go Env Var Default Value
tags: 
---

# Go Env Var Default Value

The `os.Getenv` function provided by `Go` doesn't support specifying a fallback option so your code that relies on env. var 

You could define a custom function that combines `os.LookupEnv` with the fallback option

```go
    func getEnv(key, fallback string) string {
        value, exists := os.LookupEnv(key)
        if !exists {
            value = fallback
        }
        return value
    }
```

once you have this function you can use it in your code

```go
    db, err := New(
		getEnv("REDIS_HOST", "localhost"),
		getEnv("REDIS_PORT", "6399"),
		"pw",
		getEnv("REDIS_USERNAME", ""),
	)
```