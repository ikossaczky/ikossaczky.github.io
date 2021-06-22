---
layout: post
title:  "Launching my blog with Jekyll"
date:   2020-12-26 13:43:49 +0100
categories: jekyll
redirect_from: /jekyll/2020/12/26/new-blog-with-jekyll.html
---
Today, I launched this new blog using the jekyll website generator and github pages. The purpose of this blog should be mainly twofold:
 - logging important notes for myself
 - while sharing them with others, for whom they might be helpful.

### Some notes on Jekyll installation
Although starting the blog on github pages with jekyll was declared to be simple, and actually really wasn't so difficult, it was also not completely without struggle.
Here is an unstructured list of advices, problems, solutions and features that I needed to get through (and that I still remember):
- Do not install jekyll directly with apt. Instead, do this:
  - Update your ruby version. For me, 2.5 works fine, and I got it following [this page](https://cloudwafer.com/blog/installing-ruby-on-ubuntu-16-18/).
  - Install ruby-dev or ruby-dev2.5 (not sure).
  - Follow [this page](https://jekyllrb.com/docs/installation/ubuntu/) to install jekyll
  - Follow [this manual](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/creating-a-github-pages-site-with-jekyll), to create github page (if you want to use jekyll on github page). However, follow only the instructions how to set up the repo. Following instruction how to proceed with jekyll did not work well for me.
  - Follow [these instructions](https://jekyllrb.com/docs/) to generate the webpage folder inside the repo (named e.g. `docs`). Then set this folder to be source folder of the webpage as explained [here](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site#choosing-a-publishing-source).
  - I think there was maybe one or two more issues (maybe because of my older linux version), but I don't remember the details.

- Set up latex:
  - Follow [this post on iangoodfellow.com](http://www.iangoodfellow.com/blog/jekyll/markdown/tex/2016/11/07/latex-in-markdown.html). Better copy both `_layouts` and `_include` folders. Put the  line 
    ```
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    ``` 
    into header in `_layouts/post.html`.
- Set up commenting via disqus (if desired): just follow [iangoodfellow.com](http://www.iangoodfellow.com/blog/disqus/jekyll/2016/11/22/adding-disqus-to-jekyll.html). As specification of url and base-url, I modified the following 2 lines into my _config.yml file:
```yaml
baseurl: "" 
url: "http://ikossaczky.github.io"
```
- Set the favicon (if desired) following the [answer to this stack-overflow question](https://stackoverflow.com/questions/30551501/unable-to-set-favicon-using-jekyll-and-github-pages): put the line 
    ```html
    <link rel="shortcut icon" type="image/png" href="/favicon.png">
    ```
to head in `_includes/head.html`, and your `favicon.png` file into the main directory.

- Change permalink format (if desired): By default, jekyll auto-generates permalinks to posts in the following format: `yourwebpage/category/year/month/day/title.html`. However, if you are not certain how to categorize your posts, this is quite impractical, as the permalink changes always when you change the category of your post, causing the old link to be invalid. Following the [jekyll documentation on permalinks](https://jekyllrb.com/docs/permalinks/), you can simply change the format by including something like this
```yaml
permalink: /:year/:month/:day/:title:output_ext
```
or this (preferred by myself):
```yaml
permalink: /:title:output_ext
```
into the `_config.yml` file. If you already have some posts, you can let the old-format links redirect to the posts with new permalinks using [this jekyll plugin](https://github.com/jekyll/jekyll-redirect-from).

- View the webpage:
  - run `bundle exec jekyll serve` in the folder with the jekyll source files to view the webpage locally
  - or commit and push the changes to view the webpage online under username.github.io.
  - you can also run `bundle exec jekyll build` and then view the website files locally in the `_site` directory. In order to generate the absolute hyperlinks correctly, you need to specify 
```
baseurl: "your/local/absolute/path/to/_site"
```
 in `_config.yml` file. However before pushing the changes to github, do not forget to set `baseurl: "/"` or `baseurl: ""`, otherwise the hyperlinks will not be correct online.

- Nice features of jekyll:
  - you commit your changes to the website via git. 
  - you can use [markdown][markdown-guide] instead of HTML. This makes writing posts pretty straightforward, with much less formatting overhead than in HTML (HTML still can be used).
  - nice clean theme & latex support ($$\int_0^1e^x=e-1$$) & language-specific code highlighting:
    ```python
    # some super-usefull python code:
    import numpy as np
    for k in range(5):
      print(np.zeros(k))
    ```

### Update: installation on Manjaro Linux
As of 22.6.2021, to install jekyll & bundler on Manjaro it was enough for me to run the following commands:
```bash
sudo pacman -Syu ruby
gem install jekyll bundler
echo 'PATH=$PATH:$HOME/.local/share/gem/ruby/3.0.0/bin' >> ~/.bashrc
echo 'export $PATH' >> ~/.bashrc
bundle install
```
[markdown-guide]: https://guides.github.com/features/mastering-markdown/
