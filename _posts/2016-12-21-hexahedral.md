---
title: "<em>Hexahedral</em>"
---

In the spirit of better-late-than-never, let me show you a game I built this past January during the Global Game Jam.

<iframe scrolling="no" src="https://matthewminer.com/hexahedral/" style="width: 100%; height: 478px;">
    <a href="https://matthewminer.com/hexahedral/">Play Hexahedral</a>
</iframe>

To keep the scope manageable while flying solo I self-imposed limitations:

1. Graphics no fancier than primitive shapes.
1. Use tech I'm already familiar with. Hold the Haskell.
1. Core mechanics complete by Friday night. Sunday reserved for polish and additional levels.
1. No skimping on nutrition and sleep. Game jams should be fun.

At the same time I wanted an end result that I could demo without embarrassment or a "this was made in a weekend, be gentle, I'm a tiny speck of dust in a vast universe" disclaimer. To that end I established four goals before starting.

## Quick Load

Progress bars and slow load times are the pits. Originally I restricted myself to vanilla JavaScript and CSS with no dependencies to see that load time *blaze*, but eventually I included [Redux](http://redux.js.org) and [virtual-dom](https://github.com/Matt-Esch/virtual-dom) to maintain sanity. End result: 60 KB JavaScript, 83 KB audio. Greased lightnin'.

## Cross Platform

At a previous jam I built [a game](https://github.com/mminer/bursting-with-colour) that you can only play using Xbox 360 controllers connected to a PC or a Mac with third-party drivers installed. *I'm* not even sure how to play it any more. This time I was determined to release something playable on any device, from the junkiest smartphone to the latest quad-SLI-magma-powered-quintillion-MHz beast of a machine. Web tech was a natural fit -- if you have a browser, you can play *Hexahedral*.

## Attractive

I restricted myself to cubes, but those cubes had to look *nice*. Liberal use of CSS transitions and a pretty theme plucked from [Adobe Color](https://color.adobe.com) was just the ticket. Nothing is more disheartening than players dismissing a genuinely fun game because it looks like a spreadsheet.

## Hours (or at least ten minutes) of Fun

Quick demos are the most one can expect from a weekend hackathon, but I wanted a game that a player could spend a half hour on. This meant lots of levels. I kept the level format simple, with ASCII characters representing the state of each square. Adding levels was as easy as chucking a new object into the `levels` array. In the end I had 30 levels with difficulty ramping up from stupid easy to moderately puzzling.

It turned out well. It's fun, a decent challenge, and it loads lickety-split on iPhones and desktops alike. As a bonus it got picked up by [Red Bull Mind Gamers](http://www.redbullmindgamers.com/game/15) where you can play it along with other solid noodle-scratchers. The source code is also [available on GitHub](https://github.com/mminer/hexahedral) for perusing / critiquing / stealing and claiming as your own.
