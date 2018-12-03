---
layout: post
title: 'Continuous Delivery for...Resumes?'
date: '2016-02-28'
author: Kevin L
thumbnail: '/images/post-generic.png'
tags:
- devops
- gitlab
- cicd
categories:
- Devops
---

If you've ever wrote a resume, you know it can be a tedious task. Microsoft word and LibreOffice both have
resume templates that act as a good starting point. Once you put all your information in, you start making
little tweaks to get the information to look perfect...

Delicate table widths, font sizes going to the fraction, .01 sized space for that perfect newline break size.

Now every time you want to add some more information, the whole thing goes out of whack.  
**We are developers. We can do better.**

I've looked at straight html - Quite a bit of overhead.  
I've looked at LaTeX - Format was boggling and GUI editors were bad.  

Then I decided on markdown. Oh boy. I was already writing my website and my blog [(this post)](https://github.com/thatarchguy/KevinLawDotInfo/blob/gh-pages/_posts/2016-02-28-continuous-delivery-resume.md) in markdown and generating them using jekyll.  
Why not do this for my resume?

I wrote up a quick [markdown file](https://github.com/thatarchguy/KevinLawDotInfo/blob/gh-pages/resume.md) and had jekyll build it to html automatically. It was as easy as dropping resume.md into the root directory of my source.
Just add this to the top of your markdown file and jekyll will make it a page:

```yaml
---
layout: resume
title: Resume
permalink: /KevinLawResume.html
---
```

Now what's cool with jekyll is you can inherit this markdown into a layout. I made a layout called `resume.html` in `_layouts/`.  
Inside of that html file I just add my custom css:

```html

<!DOCTYPE html>
<html>
  <link rel="stylesheet" href="{{ "/css/resume.css" | prepend: site.baseurl }}">
  {% raw %}{{ content }}{% endraw %}
</html>
```

Now we have styling!  

So when jekyll builds my static site, it's also building my resume into html.

But how many employers accept html as a format? *None*.  
If we are already building into html, can we build into a pdf? *Yes!*  

An important lesson:  
**Do not use any percentages or responsive design in the css**

I couldn't get any markdown -> pdf libraries to format properly.  
Here's what I tried:  

```
gem install gimli
gimli -s css/resume.css resume.md _site/KevinLawResume.pdf
```
Things were not formatted properly and I kinda just gave on tinkering with it.

I decided to use gimli's base library, wkhtmltopdf and see if I could convert jekyll's html output to pdf.

html -> pdf  

```
wkhtmltopdf --user-style-sheet css/resume.css _site/KevinLawResume.html _site/KevinLawResume.pdf
```
..and it worked!  
We load the custom stylesheet in because the css link in the html layout is relative to the webserver and we aren't running the webserver when running this command.  

**Note:** Use the system package wkhtmltopdf and not RubyGem's wkhtmltopdf. The one in RubyGems is very out of date.

So how do we deploy this thing?  

I had my site running on github since it has free static site hosting using jekyll (the only reason my site is in jekyll).  
Well, they don't allow custom build steps like wkhtmltopdf.

Turns out, gitlab.com just added gitlab-pages with SSL support and any static site generator you want, as long as it writes to `/public` folder.  
I mirrored my repo over to gitlab, and here is my full `.gitlab-ci.yml` file.

```yaml
image: ruby:2.1

before_script:
  - apt-get update -qq && apt-get install -y -qq wkhtmltopdf xvfb
pages:
  script:
  - gem install jekyll jekyll-gist redcarpet
  - jekyll build -d public/
  - xvfb-run wkhtmltopdf --user-style-sheet css/resume.css public/KevinLawResume.html public/KevinLawResume.pdf
  artifacts:
    paths:
    - public
  only:
  - gh-pages
```

I use xvfb because wkhtmltopdf needs a display server to render the page.  

I plan on adding htmlproof to my build step to lint the build for code quality. And one day fixing the formatting so gimli can build from markdown instead of jekyll + wkhtmltopdf.  

Hosted on [gitlab](https://gitlab.com/thatarchguy/KevinLawDotInfo) and [github (mirror)](https://github.com/thatarchguy/KevinLawDotInfo)
