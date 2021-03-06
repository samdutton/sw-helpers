---
layout: index
title: Using sw-cli
navigation_weight: 0
---
 
# Getting Started

If you are completely new to service workers and sw-helpers,
the easiest place to start with `sw-cli`.

This is a command line tool that can build a service
worker that will precache the assets for your site so that
it can work offline.

## Install

1. [Install Node.js](https://nodejs.org/en/).
2. Install `sw-cli` using NPM.

```
npm install -g sw-cli
```

## Generating a Service Worker

To generate a service worker for your project just run
the CLI with the 'generate:sw' command in the root of
your poject.

```
sw-cli generate:sw
```

This will ask a range of questions about your web app like
which directory contains the assets for your site, which assets
you'd like cache etc..

![Screenshot of the sw-cli command.](./images/sw-cli-questions.png)

After the command has run you'll have two new files, a
`sw-lib.vX.X.X.min.js` and `sw.js` file (unless you changed
the file name).

To use the service worker, you'll need to register your
newly generated service worker file in your web page,
which you can do like so:

```
if(navigator.serviceworker) {
  navigator.serviceworker.register('/sw.js')
  .catch(function(err) {
    console.error('Unable to register service worker.', err);
  });
}
```

With this, the browser will register your `sw.js` which
will preache the assets in your app and serve them
from the cache.

## What is in sw.js?

For those who are curious, let's look at whats in the
generated service worker.

```
importScripts('sw-lib.v0.0.13.min.js');

/**
 * DO NOT EDIT THE FILE MANIFEST ENTRY
 *
 * .....
 */
const fileManifest = [
  {
    "url": "/index.html",
    "revision": "b3e78d93b20c49d0c927050682c99df3"
  },
  {
    "url": "/images/hamburger.svg",
    "revision": "d2cb0dda3e8313b990e8dcf5e25d2d0f"
  },
  ....
];

self.goog.swlib.cacheRevisionedAssets(fileManifest);
```

The service worker imports the `sw-lib` file which is
a library that manages the precaching and returning
of assets when the browser requests them.

The `fileManifest` is an array of all the assets in your
web app. Each file entry consists of a URL and a revision.
This is used to download the files whenever they change.

The service worker then calls
`goog.swlib.cacheRevisionedAssets()` which will download
all the assets during the service worker install event.

## What Next?

If you only need precaching and serving of assets then
you can carry on using the CLI as is.

Otherwise maybe you'll want to explore one of the following.

### Generating SW in a Build Process

If you have a build process, you might want to use the
`sw-build` module instead of using `sw-cli`.

It allows you to generate the service worker  
programmatically.

[Learn More About sw-build Here](../reference-docs/stable/latest/module-sw-build.html#main)

### Precaching in Your Own Service Worker

Instead of generating a service worker, you may want some
of the features provided by `sw-cli` but added to your
own service worker.

For this you have a few options.

Both `sw-cli` and `sw-build` can be used to produce a
list of assets in your web app with the revision
information which can be used for precaching. We have
[recipes on how to do this](../recipes#main).

You can then use `sw-lib` to perform the precaching and
you can define custom routes and add anything else you
desire to your service worker.

[Learn More About sw-lib Here](../reference-docs/stable/latest/module-sw-lib.html#main)
