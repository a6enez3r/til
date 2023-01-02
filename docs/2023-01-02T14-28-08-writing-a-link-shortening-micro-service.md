---
layout: default
date: 2023-01-02T14:28:08-05:00
title: Writing A Link Shortening Micro Service
tags: 
---

# Writing A Link Shortening Micro Service

A link shortening service should :-

- generate a unique short alias for a link
- automatically redirect users to the original link

and optionally provide :-

- the ability to specify a lifetime on link creation
- statistic about the links

## General Calculations

For estimations we just assume some number n, number of requests we expect to get. For each request there will be two distinct operations :-

- looking up what URL a given link redirects to
- the actual redirecting operation itself

While the creation of the shortened link happens once (& is updated rarely if ever) the redirection happens a lot more; for now we can assume for every link created there are `10` redirects a second. If there are `100k` links created a month that means we'll have `100 * 30 * 24 * 60 * 60 ~= 275,000,000` redirects a month.

If the maximum size for a given link is `5k` characters, we will need `.5GB` storage per month.

## Hashing

Since we want this to be a link shortening service we expect the generated link to be shorter without sacrificing uniqueness. If we use alphanumeric characters only to maintain readability we have :-

- `62 ! 5 = 9 billion`
- `62 ! 7 = 3 trillion`