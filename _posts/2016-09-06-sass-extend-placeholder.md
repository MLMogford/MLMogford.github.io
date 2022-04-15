---
layout: post
title: "SASS - Extend and Placeholder"
excerpt: "extend and placeholder concepts on SASS"
tags: [scss, sass, html, css, wrap]
comments: true
---

My goal is to post a few front end related posts.
When talking about SASS, it's important to make sure the concept of @extend and %placeholder are well understood.

* **@extend** is a way the preprocessors of CSS use to make a reference to previously defined style.
This is a good [reference](https://css-tricks.com/the-extend-concept/), however the idea is pretty much:

Sass:

```scss
.moo {
  margin-top: 10px;
}
.foo {
  @extend .moo;
}
```
CSS:

```css
.moo, .foo {
  margin-top: 10px;
}
```
<hr>
* **%placeholder** is a selector that define a style, however difference from others it will not be present on the generated CSS, only the selectors that @extend-ed it. Here is a good [reference](http://thesassway.com/intermediate/understanding-placeholder-selectors).

Sass:

```sass
%bar {
  margin-top: 10px;
}
.lol {
  @extend %bar;
}
```
CSS:

```css
.lol {
  margin-top: 10px;
}
```

Cheers.
