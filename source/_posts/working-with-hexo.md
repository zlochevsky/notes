---
title: Working with Hexo
date: 2024-12-21 16:59:10
tags:
- hexo
categories:
- static web
---
Working directory: `~/notes`
    
Edit content files: 
```
$ vim source/_posts/<directory>/<filename>.md
```

Create new draft:
```
hexo new draft <filename>
```

Start hexo server to show up drafts:
```
$ hexo server --draft
```

Publish draft:
```
$ hexo publish <filename in the drafts dir>
```

Create non-blog page:
```
$ hexo new page <name>
```


To create drafts by default an option in `_config.yaml` file needs to be changed:

`default_layout: draft`

So by typing `hexo create new <filename>` Hexo will create _draft_ istead of  _blog post_.


Regenerate public directory and upload to site:

``` bash
$ cd ~/notes
$ hexo generate && . ~/.local/bin/znotes
```

Content of `znotes` file:
```
rsync -t /<homepath>/notes/public/* \
<username>@<site or IP>:/var/www/<site>/www/notes/
```
