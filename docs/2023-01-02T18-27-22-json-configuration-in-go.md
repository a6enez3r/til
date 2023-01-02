---
layout: default
date: 2023-01-02T18:27:22-05:00
title: JSON Configuration In Go
tags: 
---

# JSON Configuration In Go

If you want to specify configuration options in one central place you will most probably use a configuration file. For example something that looks like :-

```
{
 "server": {
   "port": "8080"
 },
 ...
 "redis": {
   "host": "127.0.0.1",
   "port": "6379"
 }
}
```

`NOTE`: the nested structure where we configure different components independently.

Once you have a configuration file in mind you can write a parser package as follows :-

```
package parser

import (
  "encoding/json"
  "io/ioutil"
)

type Config struct {
  Server struct {
     Port string `json:"port"`
  } `json:"server"`
  ...
  Options struct {
     Schema string `json:"schema"`
     Prefix string `json:"prefix"`
  } `json:"options"`
}

func FromFile(path string) (*Config, error) {
  b, err := ioutil.ReadFile(path)
  if err != nil {
     return nil, err
  }

  var cfg Config
  if err := json.Unmarshal(b, &cfg); err != nil {
     return nil, err
  }

  return &cfg, nil
}
```
