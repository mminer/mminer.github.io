---
title: "Dark Mode CSS"
---

In time for Halloween but late for the 20<sup>th</sup> anniversary of The Smashing Pumpkins' *Adore*[^1], Apple added support for dark mode on the web via the [`prefers-color-scheme`](https://drafts.csswg.org/mediaqueries-5/#prefers-color-scheme) media query in Safari Technology Preview. Lately I've noticed developer-centric sites like Docker and Microsoft's docs offer a dedicated switch to toggle between light and dark themes, but this CSS-only solution is way easier.

```css
@media (prefers-color-scheme: dark) {
    body {
        background: black; /* like my soul */
    }
}
```

Implementing dark mode support on this blog was a breeze thanks to its grand total of 13 colours. I took the lazy route and inverted most of them using Sass' [`invert`](http://sass-lang.com/documentation/Sass/Script/Functions.html#invert-instance_method) function. The end result looks respectable given the minimal effort expended.

![Dark mode CSS comparison](/images/dark-mode-css-comparison.png)

I doubt we'll see widespread adoption of `prefers-color-scheme` --- providing multiple themes would be a serious undertaking for many websites --- but it'll be useful for web apps that strive to honour the user's preferences. Because the customer is always right, power to the people, yadda yadda yadda.

---

[^1]: This was the best goth reference I could think up. I'm not even sure it counts. I missed the goth boat in my youth.
