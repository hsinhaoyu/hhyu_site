---
title: "About this site (a tutrial on building a personal website)"
date: 2020-12-17
draft: false
tags: ["tutorial", "hugo"]
---
I started this website in 2018, because I wanted to collect all my content in one single place. I decided to built it with simple tools, so I could understand how everything works, and fix problems if needed. It's a hobby like how some people like to build their own kitchens. Lots of tutorials are available on building personal websites, but I still want to write my own, because when I got started, I could never find one that explained every step needed to build a complete website. It's not that I couldn't find information about how to do certain technical steps. The problem was that I wasn't sure which steps were relevant. So I will document everything I have done for this site, and I hope that it could be helpful for those who want to hand-build their websites.

### Static website generation with [Hugo](https://gohugo.io)
The easiest way to make a website is to use a static website generator. The way it works is that you provide a folder of documents written in a [Markdown](https://www.markdownguide.org), and the generator makes a website for you based on the theme that you choose. The generated website is just HTML, css, and javascript files. There is no content management systems and no databases. For the simple website that I have in mind, it's more than enough. But of course, it means that it's harder to include fancy features such as webstores, discussion forums, and other forms of interactivity. For that kind of thing, you are probably better off with Wordpress or SquareSpace.

There are many static website generators to choose from. In fact, the number is growing so fast that it's hard to keep track of them. I picked [Hugo](https://gohugo.io) because it's easy to understand and easy to customize. It's written in the [Go programming language](https://golang.org), which tends to encourage minimalist design. Another feature that I found very attractive is that although it doesn't use a content management system, it is very flexible in content organization. You can organize your content based on tags or categories, but can you also define your own taxonomy, which can be used to implement database-like features. Simple websites are easy to build with Hugo. It takes very little time and effort to get something basic running. The most important thing is to pick a theme. I picked a minimalist theme called [Pickles](https://themes.gohugo.io/hugo_theme_pickles/) and never look back, but there are many [options](https://themes.gohugo.io). 

The Pickles theme, like many other Hugo themes, supports the display of LaTeX equations using the [KaTex](https://katex.org) fast rendering engine. The instructions, however, are hidden in the theme's demo page ([here](https://themes.gohugo.io//theme/hugo_theme_pickles/post/math-typesetting/)) so you have to look carefully.

The only thing that I find lacking in Hugo so far is the presentation of images and image galleries. I use a package called [Hugo Easy Gallery](https://github.com/liwenyip/hugo-easy-gallery), but I haven't customize it enough to make images look nice. This requires some more work.

### Tweaking the Hugo theme to support two blogging formats
I wanted a website that has two sections: a [regular blog](https://hhyu.org) for essays, and a [microblog](https://hhyu.org/micro) for notes. Many people like to have a section of tweet-like short content. But for me, microblog entries are not necessarily shorter. Rather, they are different from regular blog entries because they don't have titles. If I can come up with a title for something I write, it means that it's an article written to communicate an idea to an audience. It's displayed with the title and a short blorb, which invite the readers to dig further. But if I can't come up with a title, it means that it's unorganized thought, written mostly for myself. It's displayed without a title, without tags, and in entirety. It's intended to be read as a person journal or a [commonplace book](https://en.wikipedia.org/wiki/Commonplace_book). 

Most Hugo themes don't have a microblog section, so I had to modify the Pickles theme. This requires understanding how Hugo organizes its content, and how the template system works. Hugo's template system is derived from the template library of the Go programming language. They can embed programming logic, which make them very powerful, but it also means there is a learning curve. I couldn't find a tutorial that explained the general idea to me, until I found a book called _Build Websites with Hugo_ by Brian Hogan. It's a little book that explains how to design a Hugo theme from scratch. Once I understood the logic behind the Hugo templates, it was surprisingly easy for me to tweak the Pickles them to support the microblog format.

In short, a Hugo website can have multiple sections, which correspond to directories in the `content/` directory. I have a `posts/` directory for the regular blog, and a `micro/` directory for the microblog. When the website is generated by Hugo, content in the former directory is displayed under `hhyu.org/posts`, whereas content in the latter is displayed under `hhyu.org/micro`. All I had to do was to give these two sections different templates. It is accomplished by adding a new template file called `section.html` under `themes/pickles_modified/layouts/_default/`. This is what's in the file:
``` go
{{ if eq .Section "posts" }}
    {{ .Render "list" }}
{{ else if eq .Section "micro" }}
    {{ .Render "list_micro" }}
{{ end }}
```
It's easy to understand. If the reader requests `hhyu.org/posts`, use the `list.html` template. If the reader requests `hhyu.org/micro`, use the `list_micro.html` template instead. `list.html` is part of the Pickles theme. I just had to modify it, and save it as `list_micro.html`.

The first time I tried to implement this idea in 2008, I decided not to modify the Pickles theme directly. Instead, I overrode some of Pickles' behaviors by saving my own templates under the `layout/` directory of my website's source code. After a while, I realized that it was easier for me to create my own version of the Pickles theme. This was done by _forking_ Pickles source code on GitHub. My forked version of the theme can be found [here](https://github.com/hsinhaoyu/hugo_theme_pickles) on GitHub.

### Website hosting with AWS [S3](https://aws.amazon.com/s3/)

So, Hugo turns Markdown documents into a bunch of HTML files. The next question is: How do we show these HTML files to other people? In other words, how to host them? Hosting HTML files is one of the most basic web services that can be done in numerous ways. I decided to use S3, the cloud object storage service of AWS. It's an overkill for my needs, but it's cheap, widely used, and it's well integrated with AWS' other services.

Hosting on S3 is simple: you first create a storage space called a "bucket", upload the Hugo-generated HTMLs into the bucket, and then make the bucket accessible to everybody. You can follow [this document](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html).

Since I need to upload to S3 every time I update my website, it's worthy to do it from the command line. For that, I downloaded the [AWS command line interface](https://aws.amazon.com/cli/). After configuring it with my login information and the bucket's location, I can synchronize Hugo's generated website with the S3 bucket using a simple `sync` command.

### Getting a custom domain name
The problem with hosting on S3 was that the website had to be accessed via an address given by the S3 server. I wanted it to be something simpler and personal. For that, I bought my domain name `hhyu.org` from one the more popular domain registrars [hover](https://www.hover.com), but you have many options to choose from. One thing that you should be aware of is that I don't actually own my domain name. It's more like a lease, so I have to renew my domain name every year.

Owning a domain name sounded like a big deal to me in th beginning. I didn't look into building my own site for many years, because I thought that only professional designers deal with registrars. But in reality, it's very simple. I logged on to my registrar, and told it to forward all "http://www.hhyu.org" traffic to the address assigned to my bucket by AWS. That's it. An interesting thing that I didn't know when I started was that http://www.hhyu.org and http://hhyu.org are two separate addresses, so I had to tell hover.com to forward both to AWS. I thought that `hhyu.org` was an abbreviated form, which is translated to the full name `www.hhyu.org` by the browser.

### Directing traffic to my domain with [AWS Route 53](https://aws.amazon.com/route53/)
With forwarding, people could use my domain name to visit my website, but the address displayed in their browser was still the AWS address. That seemed wrong to me. I don't want my readers to feel that they started at `hhyu.org` but were transported to a different place. In other words, what I wanted was "address masking" rather than "forwarding". 

There are different ways to achieve this goal, but since I use AWS S3 for hosting, I decided to use another AWS service called Route 53. The process is a little technical, involving editing something called DNS records, but it was just a matter of filling up some forms. I followed the instructions of [this tutorial](https://aaronshaver.medium.com/setup-a-custom-domain-on-aws-using-s3-static-website-hosting-and-route-53-1b30eda0d4c1). What got me stuck for a while was I didn't understand the logic behind it. The basic idea is this: DNS (Domain Name Service) is the mechanism for translating domain names such as `hhyu.org` to the actual addresses of services on the Internet. My registrar's DNS only does forwarding, but I can tell it to use Route 53 instead. Once I allowed Route 53 to provide the DNS service, it can make it look like my website and my domain name are one and the same.

### Securing content distribution with [AWS CloudFront](https://aws.amazon.com/cloudfront/)
The hosting service of S3 is convenient, but it serves the web pages with the HTML protocol. It's an unencrypted protocol that can easily be exploited. To increase the security of my site, I'd like to use the HTTPS protocol instead. Within the AWS ecosystem, this can be done with CloudFront. I followed this [tutorial](https://www.davidbaumgold.com/tutorials/host-static-site-aws-s3-cloudfront/). There are two major steps: First, you have to create a "distribution". CloudFront is a content distribution network that sends your content to AWS sites all over the world, so you have to tell it to make a distribution for your bucket. Your distribution needs an ACM security certificate. Getting a certificate sounded like a difficult process, but it really was just a matter of sending a request on AWS. It was granted in less than an hour. One problem that I encountered was that I didn't know that I had to switch my region to North Virginia when I requested a certificate. 

Big distribution networks like CloudFront move slowly, which means that after you update your bucket, it can take several hours to a full day for CloudFront to update your content. It isn't a big issue for simple websites like mine, but you can _invalidate_ your distribution, to force CloudFront to update. Invalidation can be initiated with CloudFront's web-based console, or with the `create-invalidation` command in the AWS commandline tool.


### Building a deployment pipeline with [GitHub Actions](https://github.com/features/actions)
At this stage, I have to manually use Hugo to generate the website, and sync the new website to S3 from the command line. This process is not very tedious, but I still wanted to automate it. For a casual blogger like me, reducing the amount of friction in the process of blogging is important for me to keep me going. Hugo has its own [deployment mechanism](https://gohugo.io/hosting-and-deployment/), but I decided to use GitHub as the mechanism for deployment. The rationale will become more obvious in the next section.


[GitHub](https://www.github.com) is a cloud service for programmers to share their source code. To maintain a copy of my website on GitHub, I use a version-control system called [git](https://en.wikipedia.org/wiki/Git) to track the changes made to my website. Once I am happy with a new edition of my website (which is typically after I add a new post), I can "commit" the edits and then "push" the source code to GitHub. `Git` is a very complicated program that takes time to learn, but the advantage is enormous: First, I always have the source code of my website backed-up on GitHub. Second, `git` maintains the entire history of my website, so I can checkout past editions of the website easily if needed. Third, thank to the branching feature of `git`, it's safe to tinker. If a new experimental feature doesn't work as planned, it's trivial to revert to a previous stage of the website. And forth, keeping a copy of the source code on GitHub opens the door to automation.

The key to building a deployment pipeline around GitHub is a concept in software engineering called Continuous Integration (CI). What it means is that once a new version of a piece of software is finalized, an automated process should check for bugs, and prepare it for deployment. For our purpose, it means that that once the source code of a new version of the website is sync'ed to GitHub, a script will automatically regenerate the website with Hugo, and deploy it to AWS. [Travis-CI](https://travis-ci.com) is a popular third-party service that can integrate with GitHub, but I decided to try GitHub's own automation tool called Actions. Because all the necessary actions have already been developed by the GitHub community, it was very easy for me to glue them together. [This](https://github.com/hsinhaoyu/hhyu_site/blob/master/.github/workflows/main.yml) is the script that I am using. It generates the website, syncs with S3, invalidates the Cloudfront distribution, and sends me a message on slack to tell me that it's all done. I can openly share this script with you, because all authentication information is encrypted in GitHub as "repository secrets", so they don't have to be revealed in the script.

With this setup, after writing a new blog post, all I have to do is to sync with GitHub. GitHub Actions takes care of the rest. 

### Posting on the go 
I don't have a lot of opportunities to blog from my laptop (such is the life of a dad with a young child), so I really needed a way to blog from my phone. The problem was that my process was built around the Unix command line. For a long time, I thought that there was no way to solve this problem... until I found [this article](http://evanbrown.io/post/hugo-on-the-go/). Since Hugo is now running on GitHub rather than my laptop, all I have to do is to use a GitHub client for the phone to post to my website. I use a program called [Working Copy](https://workingcopyapp.com/) for the iOS. It's a little expensive, but it's a very well developed app. I can create a new Markdown file in the `content` directory of my website's repository, commit the changes, and push to GitHub, and then the website will be regenerated and deployed. 

Note that since I still use my laptop to update my website, it's important to make sure GitHub Actions uses the same version of Hugo that I use on my laptop.

### Stepping into the IndieWeb
[IndieWeb](https://indieweb.org) is an alternative approach to social networking. In contrast to corporate-owned services such as Facebook or Twitter, IndieWeb websites are owned by individuals, and they interact with each other using open protocols. It is an exciting movement that deserves a more in-depth discussion (you can start [here](https://www.smashingmagazine.com/2020/08/autonomy-online-indieweb/)), but for the purpose for this tutorial, I will concentrate on posting to this website easier, using tools developed by the IndieWeb Community.

### Configuring [IndieAuth](https://indieauth.com) for authentication 
TODO


### Deploying a IndiePub server on [AWS Lambda](https://aws.amazon.com/lambda/)
TODO


SAM Local: https://aws.amazon.com/blogs/aws/new-aws-sam-local-beta-build-and-test-serverless-applications-locally/
