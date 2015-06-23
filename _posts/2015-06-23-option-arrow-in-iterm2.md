---
layout: post
title:  "Using Option + Left or Right Arrow Keys in iTerm2"
date:   2015-06-23 10:10:57
categories: jekyll update
---

I absolutely adore [iTerm2](https://www.iterm2.com/downloads.html).  So far, I haven't found a better terminal, nor do I think there is too much they could improve upon here (there's always little things - but these guys have the overwhelming broad-strokes correct)

Using Terminal.app, I grew to depend on option + left arrow or option + right arrow to move the cursor by word instead of single character.  It's much quicker to navigate to some mispelling in a long bash script when you have this key mapped.

Could NOT figure out how to get it working in iTerm2.  But finally, chanced upon [a post](http://brettterpstra.com/2011/08/12/option-arrow-navigation-in-iterm2/) that documented one solution.  Wasn't thrilled with what appeared to be a hack, but luckily Scott Lee in the comment section [posted a link](http://cl.ly/3N2t2i2C0G2I1F0B2y2w%29) to a screenshot of his keymappings.  He is using the CloudApp service to host that screenshot, and while I've linked to it, if for any reason the link ever goes down, I saved it and am uploading it to GitHub hoping it will never be lost :)  

<img src="/assets/iterm2-keys.png" />

The way to setup these two shortcuts:

1. Click on iTerm to bring it to focus and either select Preferences from the toolbar or use ⌘, (command + comma) to open the Preferences pane (ProTip: ⌘, opens the Preferences window for any Mac application)
2. Click on the Keys icon in the Preferences window
3. Click the + button at the bottom of the list of existing keyboard shortcuts to add a new shortcut
4. Press the shortcut sequence you want for this new shortcut in the first box - for me it's option + left arrow (⌥←)
5. From the Action drop-down, select "Send Escape Sequence"
6. Now in the box that appears, type "b" (lower-case, no quotes)
7. Click OK

Repeat the sequence for the right arrow key, in step 6, use "f" instead of "b" (forward and back, respectively)

Close the Preferences pane and the keyboard shortcut should function immediately without restarting iTerm 2.