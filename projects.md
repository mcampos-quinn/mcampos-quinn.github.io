---
layout: page
title: Projects
permalink: /projects/
vimeoID: 407474921
---

Here are some recent personal and work projects I'm particularly fond of. I'll add more as I have time and inclination 😃

[Untitled birthday series](#at-four-at-six)

[Blacklight](#bampfa-customizations-to-blacklight)

[heartfilm](#heartfilm-<3)

[garden cycle](#garden-cycle)

[TVTV](#tvtv)

---
# AT FOUR, AT SIX...

2020, 2022

Inspired by [Gunvor Nelson](https://vimeo.com/242768525), Lynne Sachs, and others, I started filming what I hope will be an ongoing series of lyrical tributes to my daughter and to our relationship as it grows and develop--as she grows into the space she will eventually fill. My heart is full <3

*AT FOUR (2020)*

<img src="/images/projects/at-four.jpg" width="500">

*AT SIX (2022)*

<img src="/images/projects/at-six.jpg" width="500">

---

# BAMPFA customizations to Blacklight

2020-

I've been working on [CineFiles](https://cinefiles.bampfa.berkeley.edu/) since I started working at BAMPFA full-time in 2013 (actually as a volunteer from 2010-2012, I clipped film reviews destined to be scanned for CineFiles...), but in 2020 we finally launched a new interface to replace the custom Java-based site that was built in 2008 and hadn't been updated significantly since.


[Blacklight](https://github.com/projectblacklight/blacklight) is an open source "discovery interface" maintained by a consortium of developers primarily at research university libraries (Stanford, Cornell, Princeton, and now... Berkeley). It uses [solr](https://solr.apache.org/) as its search engine and is fairly agnostic about the kinds of data that can be included in a given collection. The heaviest users are major university libraries, but (thanks to [Research IT](https://research-it.berkeley.edu/) 🥺) the UC Berkeley museums started using it in 2018 or so with the Hearst Museum getting the first live instance. Working with Research IT taking the lead I was able to do a little customization to the logic (in Ruby) and a little design work and we launched the new site right at the beginning of COVID!

In 2022 I also helped make a new site for the BAMPFA art [collection](https://collection.bampfa.berkeley.edu/), which I'm also pleased with. I got to do a little more code customization, and for instance there's a randomizer that refreshes the landing page with a new selection of images from the collection every time you load the page–simple, but effective!

<img src="/images/projects/bampfa.png" width="800">

github repo [here](https://github.com/BAM-PFA/radiance)

---

# heartfilm <3

2020

github repo [here](https://github.com/mcampos-quinn/heartfilm)

![heartrate activated shutter trigger](/images/projects/heart.gif)

In process! I also don't think the title of the film will actually be *heartfilm* but it's fine for now. This is intended for a diary/self portrait film of reflection during COVID-19. **Update** I recognized after I wrote this that my COVID reflections as a supremely privileged white-ish person were hardly noteworthy, but I'm still looking for a way to use this technique that's engaging and rigorous (enough).

I'm using an LED pulse sensor to drive a GPIO pin on an Adafruit *ItsyBitsy* microcontroller. When my pulse triggers the monitor, the GPIO pin goes to ground and completes the circuit on the single-frame shutter trigger. Voilà! Here's a circuit diagram:

<img src="/images/projects/heartcircuit.jpg" width="400">

Other ideas of course involve using the trigger to film something based on some stream of data. It's pretty open ended!

---

# Garden cycle

2020-

I've been shooting [super8](https://vimeo.com/392847863/685b8329f7) and [16mm](https://vimeo.com/397312531) film in the UC Berkeley Botanical Garden. I'm not... totally sure what I will end up with yet, but I had this vision in my head for years of a stop-motion flicker film of just blossoms.

![succulents](/images/projects/succulents.gif)

That isn't entirely how it has worked out so far. One of the threads I've been exploring is the impermanence of human labor, so there are contrasts between the labor of the garden as an arm of colonialism and its placement of Ohlone grinding stones (like the one below) in a kind of half hearted anthropology.

[![link to garden film](/images/projects/grinding.png)](https://vimeo.com/506669957)

---

# TVTV

2018-

This is such an incredible [project](https://bampfa.org/news/neh-grant-will-support-preservation-tvtv-archives) for me to work on now, since it spans basically my entire professional career. One of the projects I worked on as an intern at the PFA Library while I was in grad school was making a [finding aid](https://oac.cdlib.org/findaid/ark:/13030/c87m0fns/) for the TVTV paper collection.

I worked on various iterations of this grant (BAMFPA applied 3 times) and wrote the metadata and preservation plans for the successful NEH grant, and have been supervising the project assistants (there have been two so far, with another on deck :/), and now am *de facto* managing the entire rest of the project. We launched it publicly with a dedicated [website](https://guerrillatv.bampfa.berkeley.edu/) that I made ahead of the 2020 presidential conventions and I covered the PR, arranged interviews with TVTV members by Steve Seid, corralled the writers we commissioned, etc. etc. There's still some left to do, though, mostly put off by COVID: a bit of remaining video digitization QC, metadata QC, getting some additional press coverage, adding more detail to the website... and so on.

Anyway I'm stoked on it as a hugely relevant set of historical documents for this time (2020 being another banner year for fucked up politics). It's a sprawling project with many moving parts (and a $250k budget), so it's been challenging and once complete will be a hugely rewarding effort. So... "type 2 fun."

---

# EDITH

2017-

[This](https://github.com/BAM-PFA/edith) was/is an incredible learning experience full of valuable lessons, not the least of which is "don't do this again." I made a digital preservation repository for BAMPFA's digital AV material, based on work at CUNY and IFI and using ResourceSpace as a front end. It works, mostly, which is great! It's also a huge technical challenge and a timesuck of as much energy as I care to throw at it.  But hey, it works!
