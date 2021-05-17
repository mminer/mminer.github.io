---
title: "RealSense Skeletal Tracking"
---

This is the [Intel RealSense D415 depth camera](https://www.intelrealsense.com/depth-camera-d415/).

![Intel RealSense D415](/images/realsense-d415.jpg)

I tinkered with one of these when I worked on [MixCast](https://mixcast.me). We used it for background removal without the need for a green screen. It's a nifty device. It sports a regular RGB camera but also tells you how far away objects are via... lasers? I don't understand how it works. It's like the Xbox Kinect when that was a thing.[^1]

![RealSense depth view](/images/realsense-depth-view.png)

On a whim I tried it for skeletal tracking. I first gave Intel's [officially sanctioned SDK](https://www.intelrealsense.com/skeleton-tracking/) a whirl. No luck. Next I tried [Nuitrack](https://nuitrack.com). Much better. I was able to open their Unity project and wire it up to an armature with minimal effort. I used characters from [The Realtime Rascals](https://assetstore.unity.com/packages/3d/the-realtime-rascals-191779), assets that Unity built to demo realtime filmmaking.[^2]

<video controls height="371" src="/videos/skeletal-tracking.mp4" width="660"></video>

You don't need a fancy doodad like a RealSense for skeletal tracking. The flagship iPhones and iPads boast Lidar sensors, so you might already own a capable depth camera. Even better, check out [this tease](https://www.linkedin.com/posts/okaysamurai_characteranimator-activity-6797534218470744064-G-cF) for no-fuss facial and skeletal tracking in Adobe Character Animator using just a webcam. The future is here.

---

[^1]: Last year Microsoft resurrected the Kinect under the [Azure banner](https://azure.microsoft.com/en-ca/services/kinect-dk/). It looks like a promising alternative to the RealSense if you need this sort of device.

[^2]: This is the real reason I dug out my RealSense, to make that frog dance.
