---
title: "OCR on the Fly with Keyboard Maestro"
---

Version 9 of automation Swiss army knife Keyboard Maestro [just dropped](https://www.stairways.com/press/2019-08-13), and with it support for OCR. I've long wanted a painless way to copy text from images to the clipboard, even futzing for a while with [SwiftOCR](https://github.com/garnele007/SwiftOCR) and [Tesseract](https://github.com/tesseract-ocr/tesseract), but now I can do it with a two-action macro.

![Keyboard Maestro OCR macro](/images/keyboard-maestro-ocr-macro.png)

`screencapture -c -s` copies a custom selection of the screen to the clipboard, the same thing as hitting <kbd>command</kbd> <kbd>control</kbd> <kbd>shift</kbd> <kbd>4</kbd>. Keyboard Maestro then grabs the image, performs its OCR magic, then slaps the result back on the clipboard.

<video autoplay height="412" loop src="/videos/keyboard-maestro-ocr.mp4" width="660"></video>

The initial results are promising. It occasionally returns gibberish --- it has difficulty processing small UI elements on non-Retina screens --- but for the mostpart it works a treat.

*[OCR]: Optical Character Recognition
