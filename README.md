This is my personal website, built with Hugo. I use GitHub Actions to generate the website and deploy it on AWS S3.

## new post
```sh
hugo new posts/filename.md
hugo new micro/filename.md
```

## added figure
```sh
{{< figure src="/blog/spiral.gif" link="blog/spiral.gif" >}}
```

## development
```sh
hugo --cleanDestinationDir
```
