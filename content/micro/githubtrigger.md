---
title: ""
date: 2020-12-05T00:44:40+11:00
draft: false
tags: ["website"]
---
My site is hosted on AWS S3. I was planning to use Travis to setup a pipeline, such that every time I push a new post to GitHub, it automatically generates the site and syncs with S3. The problem was that Travis’ cost structure seems to have changed, so it became less appealing. I gave GitHub Actions a shot. It worked very well. It’s easy to use and easy to program. 
