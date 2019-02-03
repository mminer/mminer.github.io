---
title: "<em>High Performance Web Sites</em> and <em>Even Faster Web Sites</em>"
---

Recently I've been diving deeper into web app performance --- how *do* browsers handle DOM layout? --- so I picked up *[High Performance Web Sites](http://shop.oreilly.com/product/9780596529307.do)* and its sequel *[Even Faster Web Sites](http://shop.oreilly.com/product/9780596522315.do)*. I still only have a hazy idea of how Chrome decides where to stick all those boxes (that's not what these books cover), but they neverthless impart juicy knowledge about making web pages speedy.

![High Performance Web Sites and Even Faster Web Sites book covers](/images/hpws-and-efws.png)

Like a fool I checked the publication date only after I started reading, which is dangerous for any tech book but especially for a topic as rapidly evolving as web development. *HPWS* was published in 2007; *EFWS* in 2009. That's like a century ago in the web dev world.

Despite this, much of the advice they divulge remains relevant, and the stuff that doesn't is a pleasant jaunt down memory lane. I'd forgotten about the many stupid hacks we once used to get sites working with reasonable performance across platforms.[^1] Stupid hacks are as necessary as ever with the advent of mobile browsers and the challenges of responsive design, but it's a reminder that web dev is a helluva lot easier than it used to be.

You would think that after a decade of progress everything would be Speedy Gonzales, but as time marches forward, so do load times and processing requirements. In 2009, *HPWS* reports that cnn.com requested 11 scripts; now it's up to 59, taking a full five seconds to load on a fast computer with a robust Internet connection. So this wisdom still deserves a read.

## High Performance Web Sites

This book is almost a teenager but it has aged well. It's organized into fourteen concrete rules to follow with performance tests to convince you of their validity. Quick read, straight to the point, no unnecessary fluff.

Some of the rules are obvious --- make fewer requests, minify your JavaScript, etc. --- but it's helpful to be reminded of how effective these techniques can be to improve load times. In the same vein, many of the rules have become standard practice that I take for granted. With bundling systems like Webpack now commonplace (at least in projects I've worked on), optimizations like minification and combining scripts are a given.

Other rules like using the `Expires` header are good reminders of tweaks that are easily overlooked. This one in particular is difficult to adhere to when you lack control over the server. This blog is hosted on GitHub Pages which provides no mechanism to specify custom headers. Returning visitors end up requesting my avatar every day despite my intent to never age. Larger sites are unlikely to use a host as restrictive as GitHub, but even when developers have full control over their environment I suspect this rule is given less thought than it deserves.

## Even Faster Websites

This one has aged less well than *HPWS*. Several chapters delve into hacks to work around browser quirks, many of which (the quirks) have long since been remedied. You can safely skip a few chapters unless you're a poor schmuck still supporting Internet Explorer 6 (my condolences if that's you).

The overview of different Comet approaches, for example, isn't useful unless WebSockets are off the table for some reason. Likewise, the chapter about domain sharding is less relevant now that browsers have raised their connections-per-domain limit. Other techniques like flushing the document head early so that scripts can load while the server computes the body will (soon, I hope) be made obsolete by widespread support for HTTP/2 and its fancy schmancy server push functionality.

The most eye opening chapter is the one on writing efficient JavaScript. Scary stuff. One idea to speed up long `if` / `else if` chains is to split it into a binary search tree of sorts, turning an O(*n*) operation into O(log *n*).

That is, convert this:

```javascript
if (value === 0) { return result0; }
else if (value === 1) { return result1; }
else if (value === 2) { return result2; }
else if (value === 3) { return result3; }
else if (value === 4) { return result4; }
else if (value === 5) { return result5; }
else if (value === 6) { return result6; }
else if (value === 7) { return result7; }
else if (value === 8) { return result8; }
else if (value === 9) { return result9; }
else { return result10; }
```

To this:

```javascript
if (value < 6) {
    if (value < 3) {
        if (value === 0) { return result0; }
        else if (value === 1) { return result1; }
        else { return result2; }
    } else {
        if (value === 3) { return result3; }
        else if (value === 4) { return result4; }
        else { return result5; }
    }
} else {
    if (value < 8) {
        if (value === 6) { return result6; }
        else { return result7; }
    } else {
        if (value === 8) { return result8; }
        else if (value === 9) { return result9; }
        else { return result10; }
    }
}
```

What a horror show. There might be legitimate scenarios where this craziness results in noticable speed improvements, but talk about a maintenance nightmare. Please don't write code like this unless you truly need to. Even more stressful is the description of unrolling loops and [Duff's Device](https://en.wikipedia.org/wiki/Duff's_device). My forays into game development bring me into contact with convoluted performance hacks like this and they make me want to nap.

It's not all dusty old tricks for long-resolved difficulties like string concatenation and trimming though.[^2] Some chapters like the one explaining how CSS selectors affect render times will remain evergreen when you need to squeeze out every last drop of performance. Keep in mind that browser developers haven't been sitting idle this past decade and some optimizations might be less impactful than they once were.

## Worth The Read?

Maybe. My advice: pick up *High Performance Web Sites*, especially if you're new to front end development. If you've been doing this for a while it might be review, but it's a fast read. Just skim *Even Faster Web Sites*. Or read the whole thing cover-to-cover, see if I care.[^3]

---

[^1]: Remember fixing IE6's lack of support for transparent PNGs with `AlphaImageLoader`? Yeesh.

[^2]: I forgot that `String.trim()` didn't always exist. JavaScript could benefit from a richer standard library, but it sure has come a long way.

[^3]: I actually do care.

*[EFWS]: Even Faster Web Sites
*[HPWS]: High Performance Web Sites
