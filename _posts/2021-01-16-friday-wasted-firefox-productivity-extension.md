---
layout: post
title:  "FridayWasted - Firefox productivity extension"
date:   2021-01-16 00:00:00 +0100
categories: javascript
redirect_from: /javascript/2021/01/15/friday-wasted-firefox-productivity-extension.html
---
I wrote an Firefox extension that helps to "waste only one day (or even only one evening) weekly by browsing unproductive pages, while blocking them for the rest of the week". This extension written javascript is loosely built upon the examples from the Mozilla [webextensions-examples repo](https://github.com/mdn/webextensions-examples). Similar extensions providing this functionality already exists, but at least I could find out, how writing browser extensions works. Moreover, this extension supports defining sites that should be blocked using [regex](https://en.wikipedia.org/wiki/Regular_expression). The code can be found in [this repo](https://github.com/ikossaczky/friday-wasted).

### For Firefox browser users:
If you use Firefox and want to free yourself from useless browsing you can download the extension [**here**](https://github.com/ikossaczky/friday-wasted/raw/master/fridaywasted-0.0.2-fx.xpi) and install it in the add-ons section [manually](https://support.mozilla.org/bm/questions/785686). And have only friday wasted :) .

### For Brave browser users (new since 11.8.2022):
If you use Brave browser download the whole code from [the brave branch of the repo](https://github.com/ikossaczky/friday-wasted/tree/brave). and use the "Load unpacked" option on the Brave extensions page (brave://extensions/) to load it into your browser. Unlike the Firefox "Load unpacked" equivalent, in Brave the extension and its options are persistent after loading from source code between the browser sessions.

After installing, the blocklist, days, and time for unblocking can be set in preferences in the Add-ons section of Firefox.
