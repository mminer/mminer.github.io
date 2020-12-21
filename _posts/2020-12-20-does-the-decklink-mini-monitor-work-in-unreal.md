---
title: "Does the DeckLink Mini Monitor Work in Unreal?"
---

Nope.

As of Unreal Engine 4.26.0 at least. This isn't unexpected; Epic's [list of supported pro video IO cards](https://docs.unrealengine.com/en-US/WorkingWithMedia/ProVideoIO/BlackmagicIOReference/index.html) excludes the Mini Monitor. I was optimistic that it would be a cheap alternative to Blackmagic's more expensive DeckLink cards for video output after I successfully used the also-unsupported Mini Recorder for video input, but no such luck.

![DeckLink Mini Mini](/images/blackmagicmediaoutput-no-configuration-found.png)

After my [post about using the Mini Recorder in Unreal](/2020/05/15/unreal-engine-video-input), several people asked about my experience before buying the card themselves. You same folks will want to avoid the Mini Monitor for your virtual production setup (though it works flawlessly for video editing and in OBS, so I still recommend it in general).
