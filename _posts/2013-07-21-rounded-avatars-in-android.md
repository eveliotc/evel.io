---
layout: post
title:  "Rounded avatars in Android"
date:   2013-07-21 22:06:54 0000
categories: android
---
<a href="{{ site.baseurl }}/wp-content/uploads/2013/07/popular.png"><img class="aligncenter size-large wp-image-1358" alt="Telly Popular" src="{{ site.baseurl }}/wp-content/uploads/2013/07/popular-1024x640.png" width="1024" height="640" /></a>

During the development of <a href="https://play.google.com/store/apps/details?id=com.telly">Telly</a> we tried different ways to achieve those rounded avatars that for some reason had become the standard way of display an user's avatar, I think there was a meeting last year and all the designers agreed that avatars had to be rounded :)

I tried three different approaches listed below, ranked in my opinion which is worst to better, having the best at the end:
<h2>The bad</h2>
This is an old one from the ages where you didn't have <code>border-radius</code> in CSS, you simply can overlap an image with alpha in the region you want to display, and match the background color in the regions you want to hide, causing the illusion of cropping like this (warning: uglified on purpose):

<a href="{{ site.baseurl }}/wp-content/uploads/2013/07/device-2013-07-21-192209.png"><img class="aligncenter size-large wp-image-1361" alt="device-2013-07-21-192209" src="{{ site.baseurl }}/wp-content/uploads/2013/07/device-2013-07-21-192209-614x1024.png" width="614" height="1024" /></a>

Without using custom views your layout would look more or less like this:

<script src="https://gist.github.com/eveliotc/6051035.js"></script>

<h3>Why is it the bad?</h3>
<ul>
<li><p>First if you look closely you could notice if you do a poor job with your resources you will get a pixelated border plus your solid colors won't even match depending on <code>Bitmap</code> configuration</p><img src="{{ site.baseurl }}/wp-content/uploads/2013/07/bad_zoomed.png" alt="bad_zoomed" width="208" height="208" class="aligncenter size-full wp-image-1365" /></li>
<li><p>Second obvious overdraw is obvious, you get it even without adding states just yet</p>
<img src="{{ site.baseurl }}/wp-content/uploads/2013/07/bad_overdraw.png" alt="bad_overdraw" width="208" height="208" class="aligncenter size-full wp-image-1364" /></li>
<li><p>Again, this is resource error prone, if you are not careful enough you could get broken resources in different densities</p>
<img src="{{ site.baseurl }}/wp-content/uploads/2013/07/bad_density.png" alt="bad_density" width="208" height="208" class="aligncenter size-full wp-image-1363" /></li>
<li><p>Finally, by just changing your background you will have to update your overlays to match that color and forget about gradients, and remember you are not supporting states just yet.</p>
<img src="{{ site.baseurl }}/wp-content/uploads/2013/07/bad_bg_change.png" alt="bad_bg_change" width="208" height="208" class="aligncenter size-full wp-image-1362" /></li>

</li>
</ul>

<h2>The ugly</h2>

So next we have a familiar one, it is widely used by several apps right now, this technique consist of using a second <code>Bitmap</code> for <a href="http://en.wikipedia.org/wiki/Mask_(computing)#Image_masks">masking</a>, whether is one obtained from resources or drawn at runtime, this technique provides much better results and you don't have to deal with resources:

<script src="https://gist.github.com/eveliotc/6051122.js"></script>

It produces a beautiful circle! 

<a href="{{ site.baseurl }}/wp-content/uploads/2013/07/device-2013-07-21-202754.png"><img src="{{ site.baseurl }}/wp-content/uploads/2013/07/device-2013-07-21-202754-614x1024.png" alt="device-2013-07-21-202754" width="614" height="1024" class="aligncenter size-large wp-image-1373" /></a>

without overdraw or pixelation, yet keep reading...

<img src="{{ site.baseurl }}/wp-content/uploads/2013/07/cute.png" alt="cute" width="208" height="208" class="aligncenter size-large wp-image-1376" />

<img src="{{ site.baseurl }}/wp-content/uploads/2013/07/regular_overdraw.png" alt="regular_overdraw" width="208" height="208" class="aligncenter size-full wp-image-1375" />

<h3>Why is it the ugly?</h3>

To be fair it is not ugly, it is quite the opposite, isn't it?, we should call it "the dirty" instead, you probably noticed we recycle the original <code>Bitmap</code> right away, the problem is also a well known, if we are using a lot of them we get good chances eventually we could run out of memory soon and get an <code>OutOfMemoryException</code>, it gets worst if we don't properly cache those computed <code>Bitmap</code>s and our animations and scrolling could get janky due garbage collector claiming memory while we continue allocating for each avatar.
 
<h2>The good</h2>

Finally visually we can't get any better than the one above, but regarding performance sure we can, do you remember <a href="http://www.curious-creature.org/2012/12/11/android-recipe-1-image-with-rounded-corners/">this Android Recipe from Romain Guy</a>?, well you bet it is about doing the same, after that we can get a nice drawable or view, that we can <a href="https://plus.google.com/113735310430199015092/posts/FZQcNW8G75K">desaturate or apply some color filters</a> and of course we can reuse like this one:

Note that both <code>View</code>s share the same <code>Bitmap</code>

<a href="{{ site.baseurl }}/wp-content/uploads/2013/07/device-2013-07-21-220144.png"><img src="{{ site.baseurl }}/wp-content/uploads/2013/07/device-2013-07-21-220144-614x1024.png" alt="device-2013-07-21-220144" width="614" height="1024" class="aligncenter size-large wp-image-1378" /></a>

<script src="https://gist.github.com/eveliotc/6051367.js"></script>

So there you have it, the rule of thumb when using <code>Bitmap</code>s (and actually any other <code>Object</code>) is to use them as few as possible but use them to delight and enchant your users, if you didn't get tired of seen my face so many times hopefully I will be publishing more recipes/tips like this if you like to <a href="http://feeds.feedburner.com/evelio">subscribe to this blog</a>, or add me on <a href="http://gplus.to/eveliotc">Google+</a>, <a href="http://twitter.com/eveliotc">follow me on Twitter</a> or <a href="https://github.com/eveliotc">GitHub</a>! where I often post <a href="https://plus.google.com/s/%23androiddev">#androiddev</a> fun :)