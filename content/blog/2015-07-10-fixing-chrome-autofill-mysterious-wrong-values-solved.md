---
title: 'Fixing Chrome Autofill: Mysterious Wrong Values Solved'
author: michael-bromley
type: post
date: 2015-07-10T14:52:17+00:00
url: /437/fixing-chrome-autofill-mysterious-wrong-values-solved
categories:
  - post
tags:
  - code
  - html

---
On an ecommerce website I maintain, we started running into a strange issue where we were getting orders coming in with the first line of the address being duplicated in the &#8220;delivery instructions&#8221; field. This was causing all sorts of confusion and mild distress at the office. Today I finally persisted enough to get to the bottom of it.

The form was something like this &#8211; try it out yourself to see if you also get the first line of your address appearing in the delivery instructions:

<p class='codepen'  data-height='480' data-theme-id='8720' data-slug-hash='vOjdad' data-default-tab='result' data-animations='run' data-editable='' data-embed-version='2'>
  See the Pen <a href="http://codepen.io/michaelbromley/pen/vOjdad/">vOjdad</a> by Michael Bromley (<a href="http://codepen.io/michaelbromley">@michaelbromley</a>) on <a href="http://codepen.io">CodePen</a>.8720
</p>

It turns out to be a Chromium issue related to the way the autofill feature decides where to put saved data. You may have wondered how Chrome and other browsers figure out where to put each line of the address. Looking at the Chromium source, we can see that it uses a series of regular expressions to figure this out. Here&#8217;s an example for how it detects where to put address line 1 (<a href="https://code.google.com/p/chromium/codesearch#chromium/src/components/autofill/core/browser/autofill_regex_constants.cc" target="_blank">source</a>):

<pre>const char kAddressLine1Re[] =
    "address.*line|address1|addr1|street"
    "|(shipping|billing)address$"
    "|strasse|straße|hausnummer|housenumber"  // de-DE
    "|house.?name"  // en-GB
    "|direccion|dirección"  // es
    "|adresse"  // fr-FR
    "|indirizzo"  // it-IT
    "|住所1"  // ja-JP
    "|morada|endereço"  // pt-BR, pt-PT
    "|Адрес"  // ru
    "|地址"  // zh-CN
    "|주소.?1";  // ko-KR
</pre>

If you look at the HTML of my example above, you&#8217;ll see that the name of the delivery instructions input is &#8220;instructions&#8221; &#8211; so why does that get picked up by the regex `"address.*line|address1|addr1|street"`?

After a little bit of hair pulling, I finally figured out that it matches against both the input name and the label. Look at the label again. There is actually a dedicated regex which looks for the label of address 1, which simply matches against the word &#8220;address&#8221;.

The solution was simple &#8211; you can try it out by <a href="http://codepen.io/michaelbromley/pen/vOjdad?editors=100" target="_blank">editing the CodePen demo</a> &#8211; I just changed the description to read _&#8220;Delivery instructions (if **location** hard to find)&#8221;_.

Then I moved on with my life. I hope this post can save somebody else an afternoon of bamboozlement. If this topic excites you, there is a riveting discussion to be found <a href="http://stackoverflow.com/a/9795126/772859" target="_blank">here on StackOverflow</a>.