---
title: "Full Indie Summit 2017"
---

[Full Indie Summit](http://www.fullindiesummit.com) is a fine conference and this year was no exception. Presentations covered a variety of topics, from technical post-mortems to business advice. Even for those only tangentially involved in game development, there was something to interest everyone. The seats were also comfortable. Very important.

![Full Indie Summit 2017 wristband](/images/fullindie2017.jpg)

## Em Halberstadt: How Sound Tells the Story of *Night in the Woods*

Game audio has long been something I neglect until the end, cobbling together a bunch of free sound effects and calling it a day. I've never won awards for sound design, and seeing a game like *[Night in the Woods](http://www.nightinthewoods.com)* that takes audio seriously highlights how much a disservice I do to the experience by treating audio as a second-class citizen.

Em and the developers evidently put an enormous amount of effort and care into making the game's lush soundscape, with audio cues written into the script and the final product packing some 5,090 individual clips. Much of the ambience was created with the human voice alone, from the wind to traffic noises to eerie suspenseful tones as you walk through the dark forest. My favourite part was the probabilistic pigeon sounds --- only some percent of the time do they manage to crack a seed open. It's this attention to detail that I never consciously notice but which makes the game feel alive.

## Jesse Ringrose: Design Challenges in Splitter Critters

The primary mechanic of *[Splitter Critters](http://splittercritters.com)* is a mind bender (and difficult to explain; you need to watch a video or play the game itself). It started as a Ludem Dare submission then shipped after nine months of endlessly refining the controls.

Jesse emphasized the importance of playtesting with real people and tweaking the UX to accommodate their expectations. One example was jumping over gaps --- the aliens weren't initially programmed to jump, but you expect them to make the leap when the destination is a short hop away. In the final game they do this, and though it's a small detail, the experience is much better because of the extra polish.

Unsurprisingly for a game where you slice potentially hundreds of sprites on the fly, some of which move and need redrawing every frame, performance tuning in *Splitter Critters* was a challenge. I can only imagine. Jiminy. It plays buttery smooth now though so I guess they figured it out.

## Jord Farrell: Feeling the Vibrations

Jord has created 250 (!) games. I doubt I've even played that many. He talked about his approach to development, deciding on a theme (e.g. capitalism) then designing mechanics that allow the player to explore this theme. Sometimes he takes a well-known game like *Pong*, strips away a fundamental aspect of it, then lets new and fresh mechanics evolve from what remains. It's fascinating to see such an experimental, iterative approach to game design.

As such a prolific game developer, it was appropriate that Jord built the presentation itself in Unity. It and [many of his games](https://mrtedders.itch.io) employ a glitch aesthetic that brings to mind pixel art on a TV from the 50s. He claims to be terrible at art, and this approach --- with reusable custom shaders doing much of the heavy lifting --- gives his work a distinctive, memorable look while leaving him free to focus on core gameplay. My own artistic chops leave much to be desired so I'll be sure to adopt this technique myself.

## Joel Green: Second Gen VR

The focus of Joel's talk was the 6DoF motion control in Cloudhead Games'
*[Heart of the Emberstone](http://store.steampowered.com/app/526140/The_Gallery__Episode_2_Heart_of_the_Emberstone/)*. He explained how the current crop of VR controllers, amazing as they are, feel like you're wearing an oven mitt; the uncanny valley of motion control. Not only that, but with VR you have to consider physical characteristics of the human body that were previously unimportant in game development. One challenge they faced was allowing players to rotate objects toward them, with the limited bend of the human wrist a hindrance. Suddenly kinesiology is relevant to game development.

The highlight of the talk was hearing about what players first do when given the power of telekinesis. Overwhelmingly they pick up objects and stack them into towers, not unlike toddlers do with wooden blocks. Tossing around ragdoll zombies is also a hit. Many game developers I talk to are excited about the visual immersion aspect of VR, but Joel was most enthusiastic about the experiences that great motion control can provide. It sounds like the Vive's forthcoming Knuckles controller is especially swell.

## Jose Palacios Vives: Steam Overview + Chat

This talk was a Q&A session for developers to ask their burning Steam questions. Jose made the case that Steam remains a great place for indie devs to distribute their games and is improving, despite the impression that the market has become too crowded for any of us poor saps to stand a chance.

I have yet to publish a game on Steam, but I get the impression that it can be a maddening experience, with audience members asking pointed questions about what Valve is doing to match itch.io's ease of distribution. For their part, it sounds like Valve is making a concerted effort to address these frustrations. A1.

## Matthew Marteinsson: Working With A Failure Based Workflow

Matthew's talk covered a lot of the same ground as Em Halberstadt's earlier in the day, demonstrating the constant experimentation and failure inherent in sound design. Interestingly, he also favours the human voice over a recording of the thing itself. There's something far more compelling about the quality of the human voice. In *[Don't Starve](https://www.klei.com/games/dont-starve)*, he first used recorded clips of a turkey for a character but found that his own impression of one was far superior (and after a live demonstration, I agree).

Matthew also touched on voiceovers in games. His general advice is to avoid voiceover if possible, with bad V.O. worse than none at all. Your friends willing to do it free are free for a reason.

## Tanya X. Short: Collaborating with Algorithms

As the founder of [Kitfox Games](http://www.kitfoxgames.com) which makes heavy use of procedural generation in their titles, Tanya had an interesting perspective on choosing and working with complex algorithms. She likened them to team members and argued that they should be chosen with the same care that you might a coworker. They can have the limited importance of an intern or they can take on much greater responsibility. Just as a crummy hire can drag down the whole team, so can employing an algorithm that hinders more than helps.

Tanya also discussed the problem of "sameness" that plagues procedurally generated content. It's dependent on your game --- some experiences will benefit from procedural generation, but for others you might be better off crafting everything by hand.

## Jerry Belich: Alternative Controllers

I've always been drawn to microcontrollers and custom interfaces to computers. With his custom-made bowtie clicker and infectious enthusiasm, Jerry effused that it's not just a fun hobby, but an effective marketing gimmick to make your game stand out at trade shows like GDC. One example he gave was *[Butt Sniffin Pugs](https://www.kickstarter.com/projects/spacebeagles/butt-sniffin-pugs)* which has perhaps the most... *unique* input method I've seen.

Making the jump to hardware is intimidating if you've never dove into it before, but Jerry's [getting started guide](https://jerrytron.github.io/alt-ctrl/) looks like a fine place to start for beginners.

## Tommy Refenes: The Marriage of Controls and Level Design

Making a *Super Meat Boy* game for mobile seems like an impossible task but that's what Tommy and Team Meat are doing with *[Super Meat Boy: Forever](http://supermeatboy.com)*. In the original they first perfected the controls before designing any levels, essentially employing a waterfall model for the game's development. They're taking the same approach with the sequel, and from what Tommy previewed it looks like they successfully whittled the controls down to their bare essence without losing what made the original so fun. I look forward to losing badly at this game once it's release (seriously, I don't think I'm cut out for the likes of *Super Meat Boy*).

## Leighton Gray: How to Write Games for the Internet Without Embarrassing Yourself

I expected a lighthearted and silly talk from the creator of *[Dream Daddy: A Dad Dating Simulator](http://store.steampowered.com/app/654880/Dream_Daddy_A_Dad_Dating_Simulator/)* but instead we got a serious trip through art history in the most informative twenty minutes of the day. Leighton defined her game as metamodernist, a cultural movement which can be summarized as "ironic detachment + sincere engagement." Whether that's the best way to describe our current meme-crazed Internet society is up for debate, but it sounds about right to me.

At the core of *Dream Daddy* is a heartwarming story about a father's relationship with his daughter. Starved of positivity in the world at large, Leighton explained, players crave wholesome, uplifting material. Despite this, the game had to be steeped in a layer of cynicism to make it palatable. This is a shame --- what's wrong with wearing your heart on your sleeve? --- but if it gets players through the door then rock and roll.

## Kelly Wallick: Indie Around the World and Beyond

I've lived in both Toronto and Vancouver where game development companies large and small are plentiful, so it was interesting learning about cities like Johannesburg and Tokyo where the indie game scene is only now starting to flourish. The latter is especially surprising given that it's home to heavyweights like Nintendo and Square. With programs like the [Indie Megabooth](http://indiemegabooth.com) that Kelly helps run, it sounds like it's a good time to be an indie developer regardless of your location.

## Summary

Excellent conference, top notch speakers. Five stars. Will go again.
