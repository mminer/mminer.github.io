---
title: "Big Image Recorder for Unity"
---

Want to record an enormous video of your Unity game? Fancy skipping 8K and going straight to 100K? Then comrade do I have a tool for you.

![8K vs. 100K size comparison](/images/8k-100k-size-comparison.png)

[Big Image Recorder](https://github.com/mminer/big-image-recorder) is a [Unity Recorder](https://docs.unity3d.com/Packages/com.unity.recorder@latest/index.html) plugin that captures an image sequence at a higher resolution than the maximum texture size. To do this it divides the camera's projection matrix into tiles and saves the renders as individual images to stitch together.

![Tile images](/images/tile-images.png)

![Tiles stitched](/images/tiles-stitched.jpg)

The plugin doesn't do the stitching itself. For that I recommend [ImageMagick](https://imagemagick.org). Here's the command to stitch 2x2 tiles:

    montage -mode concatenate -tile 2x2 *.png out.png

To run this command automatically each frame, enter it in the *Stitch Command* field in the Recorder UI.

![Stitch command field](/images/stitch-command-field.png)

The final images are massive. A 61,440 × 34,560 PNG clocked in at 900 MB. Granted, PNG isn't the most efficient format, but there's no escaping that excessive resolutions eat hard drive space like the Cookie Monster.

Another inherent problem is that dividing a projection matrix and stitching together the result works poorly with post-processing effects. Vignette, for example, gets applied after the camera renders and leaves noticeable seams where the edges meet.

![Stitch vignetting](/images/stitch-vignetting.jpg)

For best results you need to turn off screen space effects. A small price to pay for spectacular 100K.
