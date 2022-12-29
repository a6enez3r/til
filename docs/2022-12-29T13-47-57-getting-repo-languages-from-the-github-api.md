---
layout: default
date: 2022-12-29T13:47:57-05:00
title: Getting Repo Languages From The GitHub API
tags: 
---

# Getting Repo Languages From The GitHub API

Can easily automate getting a summary of your personal projects on GitHub using the GitHub API.

- To get all your repos
```
    gcli = Github(pat)
    try:
        github_repos = list(gcli.get_user().get_repos() )
    except GithubException:
        github_repos = []
```
- You can focus on a specifc type of repo (for instance if you want to show data only for public projects)
```
    filtered_github_repos = []
    for repo in github_repos:
        if (
            repo.owner.login == username
            and not repo.private
            and repo.name != username
            and not repo.fork
        ):
            filtered_github_repos.append(repo)
```
- For some weird reason the languages for a repo aren't included in the original API call so you have to make a separate call
```
    headers = {"Authorization": f"token {pat}"}
    resp = requests.get(repo.languages_url, headers=headers)
    languages = []
    if resp.status_code == 200:
        languages = list(resp.json().keys())
```