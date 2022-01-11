---
title: "Stable CI/CD is not a Myth, From Nexus-Jenkins to Github Packages-Actions"
categories:
  - Posts
header:
  overlay_image: /assets/images/sunset-g1a4f4fbf3_1920.jpg
  caption: "Photo credit: [**Pixabay**](https://pixabay.com/)"
tags:
    - CI/CD
    - pipeline
    - development
    - jenkins
    - github
    - build
classes: wide
author_profile: true
excerpt: ""
---

This article provides an overview and learnings about why migration from own hosted CI/CD to SaaS CI/CD, is a way to go!

## Let's first understand the history

Software Engineers from Generation X, Millennials generation must have at least once in their life worked with Jenkins (Back in time was Hudson). Java Stack CI/CD evolved around Jenkins. Most of us atleast once used Jenkins. And  Artifactory later Nexus perfectly complements each other.

## How our Self-hosted Jenkins and Nexus setup could look like.

Jenkins is mostly used for CI/CD, and in some cases as a Batch processor. To satisfy this requirement self hosted Jenkins instances running over AWS or self hosted pods on Kubernetes(K8S), mainly in MASTER -> SLAVE (agents) configuration is a correct choice.

Nexus instance can also be self hosted over AWS or with K8S.

Some of many problems with this setup and some solutions to fix some of them. 

### Problem 1. Security
 - Solution 1: 1st approach to make it secure is to use, **VPN**. Of course that makes it secure on the cost of performance plus overhead maintaining VPN instance as well.
 - Solution 2: To over come Open VPN issues, Client Side Certificates can be used. This setup involved writing a small code that will create a certificate and pushing the information to the certificate repo. But maintaining the certificate is kind of painful task.

### Problem 2. Random Jenkins restarts, auto updates
 - Solution:  Disabling auto update feature.

### Problem 3. Outdated Plugins  
- Solution: Jenkins community is not growing these days, as other communities so some the plugins are not maintained and they dont work with new versions of Jenkins. So sticking to outdated plugins makes sense.

### Problem 4. Pipeline builds
- Again maintenance and writing `Jenkinsfile` without much of documentation is kind of hard.

### Problem 5. Nexus maintenance is nightmare
- DevOps team has to be own their toes , to patch and fix the Nexus instance, as storage could often be a issue. So storage has to be maintained manually. Plus deletion of old artifacts is also one more problem to tackle.

Last but not the least, Jenkins will fail when its needed the most. As most of the teams would rely on Jenkins for CI/CD these problems can grow exponentially and the maintenance causing DevOps precious time. 

To make everyone's life easier moving to **GitHub Actions and GitHub Packages** could be a solution!

## How CI/CD with GitHub Action could look like.

The advantage of migrating to GitHub Action is to use **YAML** instead of different way of defining the Job with Jenkins. Those YAML files are maintained under `.github` folder under the respective project that gives the ownership of the flows to the Project/Module owner. Below are some sample workflows that can be defined.

1. `Build Develop/Main` - For manually building `develop` or `main`.
2. `Build PR` - When ever a PR is raised for a merge on `develop` or `main` then this workflow is executed. This is runs whenever there is commit on the branch.
3. `Publish` - After successful build of `develop` or `main`, built jar is pushed to GitHub Packages.
4. `Release Manual` - Manually releasing & tagging a fixed version.
5. `Auto Deploy API to DEV` - After successful built of `develop` or `main`, changes are deployed to DEVELOPMENT environment.
6. `Deploy API to ENV` - Manual Deployment of fixed version of API to different environments.


## Some Learnings after migration to GitHub Actions and Packages.

- Stable CI /CD is not a Myth!
- Now the project CI/CD is maintained by respective team, instead DevOps team.
- Saved a lot of developer time, troubleshoot the issues with Jenkins and Nexus.
- Saved resources as GitHub instance is used instead of self hosted.
- Cost effective, as most basic subscription of GitHub provides access to Actions and Packages.
- Stable and efficient as compared to Jenkins and Nexus.
- Large Marketplace.
- Large Developer community, more and more free actions (e.g - [Slack Notification](https://github.com/rtCamp/action-slack-notify) etc.) available.
- Very short migration time. 

## Conclusion

Migration to GitHub Actions and Packages could save lot of frustration with Jenkins-Nexus combo. This will give you a chance to try out latest tools rather than being with old and outdated technologies. The process is may not be ideal for everyone, but it solves most of the issues with Jenkins. 

As I said earlier, Stable CI/CD is not a Myth!

## References

- [Migrating from Jenkins to GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/migrating-from-jenkins-to-github-actions)
- [Introduction to GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions)
- [Github Actions or Jenkins? Making the Right Choice for You](https://blog.bitsrc.io/github-actions-or-jenkins-making-the-right-choice-for-you-9ac774684c8)
- [Slack Notification](https://github.com/rtCamp/action-slack-notify)
- [GitHub Marketplace](https://github.com/marketplace?type=actions)
- [Photo by Aleksejs Bergmanis from Pexels](https://www.pexels.com/photo/aerial-photo-of-buildings-and-roads-681335/)

