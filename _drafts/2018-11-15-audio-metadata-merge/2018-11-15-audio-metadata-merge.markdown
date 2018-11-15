---
layout: post
title:  "Merging descriptive metadata sources with OpenRefine, Python, and MySQL"
date:   2018-11-14
categories: metadata openrefine
---

*I recently had a chance to work with a collection at BAMPFA that I find really fascinating and that is definitely under-utilized (and also basically undiscoverable at the moment). We have a collection of audio recordings going back to the 1970s that is described in disparate data sources including a paper card catalog (fuck yeah!) and I took a deep dive into merging and normalizing these sources. [Here's](https://github.com/BAM-PFA/audio-database-merge) the Github repo for the scripts and data involed.*

{:refdef: style="text-align: center;"}
![akira kurosawa at pfa](/images/2018-11-15-audio-metadata-merge/kurosawa.jpg)
*Here is Akira Kurosawa chilling at the PFA. Image courtesy Berkeley Art Museum and Pacific Film Archive.*
{:refdef}

Since about the mid-1970s (we have some earlier recordings, but it was inconsistent then), the Pacific Film Archive has been recording guest speakers related to its acclaimed film programming. These have mostly included filmmakers and film critics from around the world, but there are outliers like the one time Angela Davis was hanging out with [Ousmane Sembène](https://www.youtube.com/watch?v=2EPMDDZa6hQ) in 1978. The early years in particular are extremely dense with top names of international and experimental cinema--Chantal Akerman, Wener Herzog, Jean-Pierre Gorin, Richard Serra, Yvonne Rainer, the Kuchar brothers were just some of the frequent guests of the late 70s. There were also visits from Soviet cinema leaders and members of the "[Third Cinema](https://en.wikipedia.org/wiki/Third_Cinema)" movement, among many others, along with deep dives into historical Hollywood genre pictures and classics (remember when King Vidor and Doug Sirk dropped by?).

Until 2006, these recordings were made on consumer-grade audio cassettes, along with a handful of reel-to-reel tapes, which clearly does not bode well for their long-term accessibility to film scholars and nerds. We have been digitizing tapes piecemeal with greater or lesser success in-house, and in partnership with projects like [California Revealed](https://archive.org/details/pacificfilmarchive?and[]=mediatype%3A%22audio%22). This fall, we applied for and received a [grant](https://www.clir.org/recordings-at-risk/) through the Council on Library and Information Resources to digitize 750 of the earliest recordings. 

A key element of this digitization project will be to align the existing descriptive metadata about the recordings with the digitized assets that we'll get from our vendor. I'm going to describe my efforts to clean the existing data about these recordings and merge it with descriptive data related to our born-digital audio recordings.

## analog-4-evr
Until some unknown time in the early 90s, there was a paper card catalog kept by whoever was responsible for keeping the tapes in a closet somewhere:

{:refdef: style="text-align: center;"}
![audio collection card catalog](/images/2018-11-15-audio-metadata-merge/card-catalog.jpg){: height="400px"}
{:refdef}

Somewhere in that distant past, the PFA film library was given control over the tape collection and someone made a database for basic information about the recordings that was on the cards (the tapes were also transferred to our film vault some time in the 90s...). That data was updated with each new recording until PFA stopped using cassettes. In 2012-ish the database was merged with a FileMaker database describing our poster collection. The basic information recorded included Speakers, Film titles being discussed/shown, date, notes, and any information about release forms signed by speakers:

{:refdef: style="text-align: center;"}
![audio filemaker database](/images/2018-11-15-audio-metadata-merge/fmp-db.png){: height="400px" }
{:refdef}
*take a good sniff of that data*
{:refdef}

Between 2006 and 2015, the born-digital audio recordings had no metadata recorded other than what was in the (helpfully very descriptive and quite consistent) filename. When we adopted a new digital asset management system in 2015, I wrote some `sed` commands to parse all these filenames and extract things like dates, film titles, and speaker names based on positional delimiters. The parsed data was imported as a CSV and added to records for the existing assets. It was far from perfect, and it was much less rich than the database information, but it was better than nothing. Slowly, staff and student workers have added data like additional film titles and release form status.

{:refdef: style="text-align: center;"}
![piction audio assets](/images/2018-11-15-audio-metadata-merge/piction-audio.png){: height="400px" }
{:refdef}
*there are still errors from the original filename parsing :(*
{:refdef}

## "OpenRefine is magic"

The CLIR digitization project seemed like an excellent time to clean up our existing data game and also merge our data sources into a single stream that can be updated both as new born-digital recordings are added and as tapes get digitized. The only problem is that the data from FileMaker and Piction had different "shapes!" Aside from having different descriptive fields, they also recorded things like repeating/multiple values differently. For example, in FileMaker, multiple values in a single field were separated by newlines, but in Piction, multiple speaker names and film titles are separated by semicolons `;`. The basic domain of the data is the same in both cases, though and the task seemed doable.

[OpenRefine](http://openrefine.org/) was a natural choice as a tool both to clean up the two data sources and to facilitate [normalizing](https://en.wikipedia.org/wiki/Database_normalization) the data. The end result will be a new database to serve as an authoritative data source/collection management system for this collection (for various reasons--institutional, political, maybe technical--we can't merge the data into our CMS for the film collection or the CMS for our art collection). This data source will also provide descriptive metadata to our new in-house [digital preservation system](https://github.com/BAM-PFA/edith), EDITH.

Once the data was exported as a CSV from both Piction and FileMaker, I created a project for each data set in OpenRefine, which I edited independently before merging. OpenRefine includes built-in tools to do things like parse a column of data and split it into new columns based on a separator in cell values (or really you could design lots of other conditions by which to parse column data...). It also includes tools to search for potential matches in slightly differing values across cells. For example "chantal akerman" and "Chantal A**c**kerman" almost certainly refer to the same person, and OpenRefine has a fucntion called "clustering" that is fantastic at finding these matches. There are several different algorithms you can use to identify matches, and several of them are adjustable in their "hunger" to find a match. I won't pretend to understand the math behind these algorithms, but they generally have to do with a computed "distance" between two strings. The algorithms differ in how they compute distances and you can use each of them to find matches that other algorithms miss (the really insane ones are the "phonetic" matches... dark magic for sure).

