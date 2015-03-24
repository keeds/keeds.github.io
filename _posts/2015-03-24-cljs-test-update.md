---
layout: post
title:  "basic setup for cljs.test (updated)"
date:   2015-03-24 23:00:00
categories: clojurescript
---

My previously documented setup for cljs.test no longer works correctly (as of r2814?) for reporting whether the tests passed or failed. To fix this you need to provide a custom reporter that somehow outputs the result for external processing. I've borrowed from examples by Antonin Hildebrand and Andrey Antukh on how you might do this using phantomjs. See [thread](https://groups.google.com/d/msg/clojurescript/Bz3oq6ME95c/fZbndAVWiioJ) for their comments and samples.

You can find the updated base setup here: [cljsinit](https://gitlab.com/keeds/cljsinit). You can clone this and run using: lein cljsbuild auto test

The only changes are to the unit-test.js to retrieve the result outputted from the customer reporter. This is the other change which is in the test-runner namespace. See [change](https://gitlab.com/keeds/cljsinit/commit/65712dcc8f11a52d0a5aafff0d7fe81e45d0706d).