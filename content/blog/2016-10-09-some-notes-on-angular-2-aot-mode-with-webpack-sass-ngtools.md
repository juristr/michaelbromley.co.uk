---
title: 'Some notes on Angular 2 AoT mode with Webpack, Sass & ngtools'
author: michael-bromley
type: post
date: 2016-10-09T07:18:10+00:00
aliases:
  - blog/551/some-notes-on-angular-2-aot-mode-with-webpack-sass-ngtools
categories:
  - post
tags:
  - Angular

---
Earlier this week I set out to convert a few of my Angular 2 apps to use Ahead-of-time (AoT) compilation. I've just been to AngularConnect and heard about a project named "@ngtools/webpack" ([video here][1]) which supposedly allows you to use the same build tools that the angular-cli project uses, even if you don't use the cli (which is the case with my stuff right now).

So I eagerly searched out[@ngtools/webpack on npm][2] and found this:

{{< figure src="/media/2016/10/ngtools-webpack.png" title="The @ngtools/webpack npm listing at the moment" >}}

No readme, no repo, nothing.

So I took a few (dark, frustrating) hours sniffing around and eventually got my apps working. When I mentioned this on Twitter there seemed to be a lot of interest, so I thought I'd quickly note down the things I found that helped me to implement AoT in my apps.

**Note:** This is not an exhaustive tutorial. I fully expect the project authors to document all this at some point, and they will do a much better job than I could. So these are just some notes to point you in the right direction. You'll have to roll your sleeves up too, okay?

## What You Need to Know

Here's what I found which lead me to figure it all out. _Note: I'm on Angular 2.0.2 at this time_.

  1. If you've not already, read the [angular.io docs on AoT][3]. This will tell you the basic requirements and concepts.
  2. The @ngtools/webpack project does not have its own repo. It is located [here in the angular-cli repo][4]. The [readme is here][5].
  3. The key to putting it all together was [this example app][6], also hidden deep within the angular-cli repo. Study it carefully. Between the webpack.config.js, package.json & tsconfig.json, it actually contains everything you need to know.
  4. You need to update to **TypeScript 2**.
  5. You need to update to **Webpack 2**. This will probably involve a few changes to your webpack.config.js file, but Webpack 2 is actually very good at instructing you on what is broken. Just read the advice in the command line.
  6. You cannot use `require('..')` to load your templates or styles in AoT mode. You'll need to convert to `templateUrl` and `styleUrls`. Don't worry though, the @ngtools/webpack plugin will handle the Sass files automatically. For things to keep working when developing in JiT mode, you'll need [angular2-template-loader][7].
  7. I had some trouble with the path to the ngfactory code when compiling with ngc: `Can't resolve './../path/to/app/app.module.ngfactory'` This seems related to [this issue][8]. The solution for me was to define the `genDir` in the webpack config rather than the tsconfig.js file.

My ng2-pagination project now includes a demo app which is set up to run in JiT mode for dev, and AoT mode for distribution. Here is the [webpack config][9] file.

&nbsp;

{{< figure src="/media/2016/10/aot-pre-1024x414.jpg" title="Chrome timeline for ng2-pagination demo app, JiT mode." >}}

&nbsp;

{{< figure src="/media/2016/10/aot-post-1024x444.jpg" title="Chrome timeline for ng2-pagination demo app, AoT mode." >}}

## Disclaimer

Even knowing the above, you will run into snags and issues along the way. Just hopefully fewer than I did. Maybe some of the above is incorrect; without official documentation, it can be difficult to know. Seems to work so far though. I expect that in the coming weeks, this article will become obsolete.

Sharpen you debugging and Googling skills and dive in. Good luck!

&nbsp;

 [1]: https://youtu.be/uBRK6cTr4Vk?t=7m59s
 [2]: https://www.npmjs.com/package/@ngtools/webpack
 [3]: https://angular.io/docs/ts/latest/cookbook/aot-compiler.html
 [4]: https://github.com/angular/angular-cli/tree/de3c27536d58a9f41418f988b70456d6bbaf24b5/packages/webpack
 [5]: https://github.com/angular/angular-cli/blob/8a5b2656ced81e84c96bac300b140179f473e2a2/packages/webpack/README.md
 [6]: https://github.com/angular/angular-cli/tree/de3c27536d58a9f41418f988b70456d6bbaf24b5/tests/e2e/assets/webpack/test-app
 [7]: https://github.com/TheLarkInn/angular2-template-loader
 [8]: https://github.com/angular/angular-cli/issues/2538
 [9]: https://github.com/michaelbromley/ng2-pagination/blob/8c133662c87ad9d489d89cd701eb5d317fc24161/webpack.config.js