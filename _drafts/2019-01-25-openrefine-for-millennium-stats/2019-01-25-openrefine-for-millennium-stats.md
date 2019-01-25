---
layout: post
title:  "Using OpenRefine to Report on Cataloging Statistics in Millennium"
date:   2019-01-25
tags: cataloging statistics openrefine millennium iii innovative interfaces
---

*I've been sitting on th


## CreateLists is terrible
The PFA library (“the UC Berkeley Art Museum and Pacific Film Archive Film Library and Study Center” aka TUCBAMAPFAFLASC) is an affiliated library in the UC Berkeley library system, which means that we use Millennium as our Integrated Library System (ILS) and catalog into UCB's [OskiCat](http://oskicat.berkeley.edu/). This is both great and really terrible since Millennium has an interface and user functionality that are stuck in 1997:

<p style="text-align:center">
	<img src="/images/2019-01-25-openrefine-for-millennium-stats/millennium-hello.png" alt="millennium screenshot" style="max-height:400px; "/><br>
	<i>The UI of a workhorse. An ugly, ugly workhorse.</i>
</p>

The main way that we interact with our records in bulk is through the "Create Lists" function within Millennium, which is a really crazy means of gathering records according to a pretty limited set of data points:

![create lists](create-lists.png)

Basically it helps you construct a (potentially very long) Boolean search to match the records you are interested in. I could go on and on about the limitations of this system (for starters there are only 100 "review files" that can be used at any given time, and 1/4 of them are reserved for use by the Systems Office to run scheduled batch processes, and each file is limited to a maximum number of records that can be included in the return set, oh and only one user can use a review file at a time; so maybe if you're lucky there will be an available file to run your search), but suffice it to say that in itself it is totally inadequate to do things like analyze statistics. At least I haven't figured out a reasonable way to do it.

## Which is where OpenRefine comes in

One really neat suggestion that came from the UCB Main Library cataloging department is that all staff should use a 956 field to record a bit of data for statistics each time that a record is created or modified in any meaningful way. This is done in a structured and regular way to provide a more reasonable means to then gather statistics when the time comes. Subfield $a includes a structured date element, $b includes the cataloger's initial code, and $c includes a code from a list that includes things like "CO" for original cataloging, "CCS" for copy cataloging with subject analysis, "MR" for regular maintenance, and "MA" for advanced maintenance.

![956 field example](956-CO.png)

Helpfully, it's easy to use a macro to insert an "MR" 956 entry. I have this one assigned to `F1`:

 `%CTRL+SHIFT+e%%PGDW%%ENTER%y956%TAB%%date |bpfmcq|cMR`

Anyway, the reporting that I need to do is typically to my boss for granting agencies who support the cataloging efforts of the PFA. This usually just means tabulating the number of films and then the number of print-based items that were added to the catalog during either a calendar year or a fiscal year (in our case that's July 1 - June 30 the following calendar year). I also report on records that were enhanced (either "MR" or "MA").

One useful part of Millennium is that you can export all the records in your CreateLists find set as a tab- or comma-delimited text file. Which means that I can export *all* of our records (we have thousands of records, not hundreds of thousands like Main...) and forget about Millennium as I process my neat text file somewhere else.

**DIGRESSION** I really wish that Millennium had an API like more modern ILS-es like the Ex Libris Alma system that also does stuff like export MARCXML. Sigh. **/DIGRESSION**

If you are not familiar with [OpenRefine](http://openrefine.org/) and yet you are reading this post, you should definitely get yourself a little familiar with it. It's an awesome tool for working with large batches of structured textual (and numerical? I wouldn't know) data. There are insane things you can do with Linked Open Data, reconciliation against various ontologies, cleaning messy data, and... I find that it's a handy tool for my statistics gathering. I won't go into the process of setting up OpenRefine or opening a project, but I can say both are quite painless (especially on Mac) and there are demos/FAQ/etc. on the OpenRefine [github](https://github.com/OpenRefine/OpenRefine/wiki/Screencasts). Basically once you have it installed you just create a project, select a few options for things like which delimiter and text encoding to use (the defaults tend to be good/correct), and have at it within a few seconds.

 *An important conceptual sidenote: Millennium has two types of records that are relevant here: item records that pertain to a single copy of a book or a film print, and bibliographic records that include the static information related to (in principle) every copy of a given edition of a work. Item records are attached to bib records and are more or less "mobile"--you can have a bib record with no items, but not vice versa. Bib records are created in OCLC as "master records" including things like authors/directors (MARC 100/700/etc. fields), subjects (600/650/etc.), and physical carrier information (3xx fields).*

 I start my stats process with an export from Millennium that includes a row for every item with PFA as the owning branch. I find that it's simpler and ultimately more useful to include everything than to rely on Millennium's opinion of when a record was created or modified. The export includes a set of fields that I have found are useful for the kind of analysis I do. This is the 956 field mentioned above (which is recorded on the bib record), the item record number, the item record call number, the item created date, and a few other bits and bobs.

![open refine items](OR-items.png)

You can see that each item gets a row in the OpenRefine display. The magic will happen with the dropdown arrow at the top of each column. The key tool that I am interested in here is the 'Custom Text Facet... '

<img src="custom-text-facet.png" alt="openrefine facet menu" width="400"/>

Facets in OpenRefine are a way to view or select a subset of your data based on some characteristic of the values in a column. The simplest example is "Text facet," which takes the text values of every cell in a column and provides you with a grouping of each time a particular unique value is found. This is really helpful if you have many data points with a few possible values, but much less helpful if each of your values is unique (if you try to throw thousands of values at it, OpenRefine will complain and tell you to go away). For example, in my item record export I have about 25,000 rows, but the "Material Type" column is limited to the MARC values a/t/m/g/c/e/j/etc/etc. For me it's simple since the vast majority of our collection is either "a" (print material) or "g" (projected medium, aka film or video). I can select "Text facet" and in the sidebar that comes up, click on "g" to view all the film records. Neat!

<img src="text-facet.png" alt="openrefine facet menu" width="400"/>

LOL at least that's how it's supposed to work. You can see some bib record numbers and some call numbers in this selection because Millennium doesn't *always* export records consistently. As far as I can tell if an item is attached to more than one bib record (for example, an item can be linked to a record for a compilation and to a record for the work cataloged as a monograph) it screws Millennium up when it tries to export them all.

Custom text facets on the other hand let you control what you are looking for rather than the blunt instrument of "show me all the values."
