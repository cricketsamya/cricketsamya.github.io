---
title: "GitHub Pull Request Template a faster way to document a PR"
categories:
  - Posts
tags:
  - pr
  - pull_request
  - github
  - template
classes: wide
taxonomy: GitHub PullRequest Template
author_profile: true
---

## Why do you need a Pull Request Template?

Pull Request(PR) are often when raised are not properly documented. The best way to document them is to use a consistent template. The Template will help the team to document the PR in a concise way. PR reviewer gets an idea about what to expect in a PR.

## How to add a GitHub PR Template?

Create a file `pull_request_template.md` inside a root folder of the repository called `./github`

```
./github/pull_request_template.md
```

This file is nothing but a template that will be shown on GitHub PR, when the PR is raised.

## Sample Template file

```
## Description
<!-- Please write a brief information about PR, what it contains, its purpose -->

## Link to Jira
<!-- If there is a ticket for this -->

## Screenshots
<!-- Please add screenshots -->

## Testing
<!-- How to test PR -->
```

<a href="/assets/pull_request_template.md" download>Click to Download</a> a sample PR Template.