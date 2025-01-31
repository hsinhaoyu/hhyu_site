This is my personal website, built with Hugo. I use GitHub Actions to generate the website and deploy it on AWS S3.

## new post
```sh
hugo new posts/filename.md
hugo new micro/filename.md
```

## added figure
```sh
{{< figure src="/blog/spiral.gif" width="80%">}}
```

The image should be public/blog/spiral.gif

## change style of code block
```sh
{{< highlight lisp "style=github" >}}
{{< / highlight >}}
```

## development
```sh
hugo --cleanDestinationDir
```

## update the site
```
git add --all
git commit -m "message"
git push
```

## recreate the hugo site
```
git clone git@github.com:hsinhaoyu/hhyu_site.git
```

This is not enough because it doesn't clone the themes.

Inside the cloned directory, go to `themes/` and then

```
git clone git@github.com:hsinhaoyu/hugo_theme_pickles.git pickles_hhyu
```
