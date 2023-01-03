---
layout: default
date: 2023-01-02T18:49:38-05:00
title: Writing Package Unit Tests In Go
tags: 
---

# Writing Package Unit Tests In Go

Given a tiny package like `config`, that parses a JSON configuration file

```
package config

import (
	"encoding/json"
	"io/ioutil"
)

type Config struct {
	Server struct {
		Port string `json:"port"`
	} `json:"server"`
	Redis struct {
		Host     string `json:"host"`
		Port     string `json:"port"`
		Password string `json:"password"`
	} `json:"redis"`
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

we can test it as follows.

- to test invalid file paths return a null configuration object and an error we can

```
func TestFromFileInvalidPath(t *testing.T) {
	contents, err := FromFile("/tmp/invalid.json")
	if err == nil && contents != nil{
		t.Errorf("Expected error while parsing config from file.")
	}
}
```

However, if we want to test valid file paths return the opposite we have to set up our tests with a valid JSON config file. Which we can do by :-

- creating a mock configuration generator function

```
package config

import (
	"encoding/json"
	"testing"
	"io/ioutil"
)

func MockConfig(path string) {
	sampleConfig := []byte(`{
		"server": {
		  "port": "8080"
		},
		"options": {
		  "schema": "http",
		  "prefix": "localhost:8080"
		},
		"redis": {
		  "host": "127.0.0.1",
		  "port": "6379",
		  "password": "supersecret"
		}
	}`)
	config := Config{}
	err := json.Unmarshal(sampleConfig, &config)
    if err != nil {
        t.Errorf("Expected error while unmarshaling config to struct.")
    }
	configJson, _ := json.Marshal(config)
    err = ioutil.WriteFile("/tmp/valid.json", configJson, 0644)
    if err != nil {
        t.Errorf("Encountered error while writing test config to file.")
    }
}
```

- we can then test valid configuration file paths don't return an error

```
func TestFromFileValidPath(t *testing.T) {
	MockConfig("/tmp/valid.json")
	contents, err := FromFile("/tmp/valid.json")
	if err != nil && contents == nil{
		t.Errorf("Encountered error while parsing config from file.")
	}
}
```
