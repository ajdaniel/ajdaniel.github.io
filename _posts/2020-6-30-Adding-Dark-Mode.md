---
layout: post
title: Adding dark mode to my site in CSS
excerpt: It's so simple!
---

If you are using an operating system that has a dark mode setting, and it's enabled, you will notice this website has a darker background, otherwise it has a lighter background. This can be easily achieved with just a few lines of CSS.

With very little effort, and using just CSS, you can easily add Dark mode to your website. You can see my changes in [this github commit](https://github.com/ajdaniel/ajdaniel.github.io/commit/66ec9aa3b47de0e4edaa38b428ed6b735465ac7a).

CSS can detect a system level dark mode setting with the `@media` query in the appropriate places.

```css
body {
  background-color: white;
  color: black;

  @media (prefers-color-scheme: dark) {
    background-color: black;
    color: white;
  }
}
```

This means without needing to implement a toggle (I mean, who's going to be changing it often? Just match the system) you can have two different versions of the site that are easy to implement and are easy on the eyes.
