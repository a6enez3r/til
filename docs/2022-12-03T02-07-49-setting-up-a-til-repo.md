---
layout: default
date: 2022-12-03T02:07:49-05:00
title: Setting Up A TIL Repo
tags: 
---

# Setting Up A TIL Repo

`TILs` are one-off notes for keeping track of various topics you have learned across languages / stacks / tools.

To set up a local `TIL` repo

- clone `TIL` generator

```
    git clone https://github.com/senorprogrammer/til.git
```

- build the `CLI`

```
    make
```

`NOTE`: if you are using a verion manager like `goenv` make sure to install version `1.17` (ran into build errors on macOS with `1.19`)

- add it as an alias

```
code  ~/.zshrc
alias til="/Users/abenezer/Code/tools/til/bin/til"
```

- create a `TIL` repo

```
mkdir -p /path/to/til
cd /path/to/til
git init
```

- run `til --help` to generate a config file (by default @ `~/.config/til/config.yml`)

```
commitMessage: "build, save, push"
committerEmail: "hi@abenezer.sh"
committerName: "Abenezer Mamo"
editor: "code"
targetDirectories:
  a: "/Users/abenezer/Code/packages/til"
```

- run `til <title>` to generate a new `TIL`


