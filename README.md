# Data Driven - A Cox Automotive Data Solutions Blog

## Building

To build locally, you can do the following:

```
# Install Ruby (>2.1.0)
curl -L get.rvm.io | bash -s stable

# Install bundler
gem install bundler

# Checkout repo and cd into directory
git clone https://github.com/CoxAutomotiveDataSolutions/datadriven.git && cd datadriven

# Checkout the revealjs repo
git submodule init
git submodule update

# Install packages (includes Jekyll)
bundle install

# Start Jekyll. This will automatically rebuild when changes are made.
jekyll serve -w
```

### Testing

You can test the build for errors locally by running the following:

```
gem install html-proofer
bundle exec jekyll build -d _site/datadriven/
htmlproofer ./_site --url-ignore "/#.*/" --file-ignore /datadriven/reveal.js/
```

## Updating

### Adding a member

Please update the file `_data/authors.yml` to add a member.

### Adding a post

You can create a post by adding a markdown file in `_posts/`.

Make sure you add the header (frontmatter) to the start of the file:

```
---
title: "Example Document"
date: 2018-02-08T08:08:50-04:00
author: Firstname Lastname
categories: [Cat1, Cat2]
---
```

#### Example posts

You can find example posts in the `_example_posts/` folder.

You can also find more example posts at this site: [https://mmistakes.github.io/so-simple-theme/](https://mmistakes.github.io/so-simple-theme/). For example, this post shows various formatting elements [https://mmistakes.github.io/so-simple-theme/markup/markup-html-elements-and-formatting/](https://mmistakes.github.io/so-simple-theme/markup/markup-html-elements-and-formatting/).

The corresponding markdown that generated the posts can be found in the `docs` folder: [https://github.com/mmistakes/so-simple-theme/tree/master/docs/_posts](https://github.com/mmistakes/so-simple-theme/tree/master/docs/_posts).

### Adding a talk

Talks can be added to the `_talks/` folder. The talks require the following frontmatter:

```
---
title: "Title of talk"
authors:
 - Author 1
 - Author 2
image:
  path: /images/streams.png
  thumbnail: /images/streams.png
theme: night
description: A brief description of the talk
```

By default, talks use the `reveal` layout in `_layouts` (for use with RevealJS), but another layout can be used/created and specified in the frontmatter as `layout: name`.
