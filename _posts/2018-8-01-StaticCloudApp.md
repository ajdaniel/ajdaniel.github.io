---
layout: post
title: Static IBM Cloud Application
---

For my mum's business, I run the website [simplyfleece.co.uk](https://simplyfleece.co.uk). It has been through various incarnations but right now, it's a simple website which I wrote in pure HTML and CSS (shocking!). Originally, when deploying to IBM Cloud, I was using Node JS and Express to serve the HTML. But this is overkill, especially when using Cloud Foundry.

#### Introducing the Static buildpack

IBM Cloud still allows users to deploy cloud applications with CloudFoundry, which I use for all of my applications. Normally I would use the NodeJS buildpack, but for simple HTML and CSS, I only need the [Static Buildpack](https://docs.cloudfoundry.org/buildpacks/staticfile/index.html) which allows simple HTML files to be served with very little config!

Simply create your website, and then create a `Staticfile` file, and push to IBM Cloud, and it's ready to go!

```
index.html
styles.css
StaticFile
```

```bash
ibmcloud app push -k 64M -m 64M my-app
```

And that's it! The static buildpack uses NGINX to serve the files, and if you need additional options, they go in the Staticfile and the options can be found [here](https://docs.cloudfoundry.org/buildpacks/staticfile/index.html). For example, my SimplyFleece site has the following options for setting the file structure, and forcing HTTPS:

```
root: public
force_https: true
```
