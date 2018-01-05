---
layout: post
title: Firebase Realtime Database
feature-img: "img/sample_feature_img.png"
---
I've used Firebase in a few of my projects, along the way I’ve learned some lessons about how best to use the platform to meet my needs. Firebase is a document-oriented / NoSQL database and thus shares many of the characteristics of those platforms but also has some unique traits of its own to learn. Here’s a quick brain-dump of what I have discovered along the way.

<h4><strong>RTM!</strong></h4>
SDK documentation is usually terrible, so many of us tend to skim for the high points and return to it later (or never).
The opposite is true here. The Guides and Samples are easy to follow and the Reference docs are both thorough and deceptively brief. Almost every time I had a question or issue, I found the solution in something I missed in the docs. I ultimately sat down and read every word straight through, and it was 3 hours well spent.


<h4><strong>Corollary: “Schema-less” does not mean brain-less! </strong></h4>

It is a common misconception that document-oriented databases make up-front planning about how data will be structured less important. This is a myth. If anything, I have found the opposite: they require more.

There is nothing unusual about saying good planning can help squeeze the most performance out of any software component. However in SQL databases it’s usually straightforward to fix planning mistakes either by just joining more tables, doing sub-queries, or doing bulk data updates. Since Firebase doesn’t have these concepts, take the time to sit down and actually model your data and access patterns ahead of time. You’ll be glad you did.

<h4><strong>Refs and simple retrievals are “cheap”</strong></h4>
In Firebase, a “ref” is like a pointer to data. It’s instinctive to want to cache or otherwise manage them, but in the current Firebase client libraries you should never do this. They’re really just wrappers around the URL references to data objects, and the event callbacks they provide access to can only have one listener at a time. If you need to reference an object from two different places, take two refs to it. It doesn’t “cost” more to do this.

A similar rule applies to data retrievals. Those coming from SQL databases are accustomed to trying to retrieve larger objects in as few queries as possible to eliminate round-trip time and query overhead. When flattening data structures, it’s tempting to copy “summary” data into multiple locations to allow a single retrieval to get everything required.

In Firebase, this is almost entirely the wrong decision. For one thing, simple key/ref-based retrievals are heavily optimized and Firebase offers massive horizontal scale for them. In my performance testing at I also found that Firebase does much better with more, smaller objects. Even removing a few unnecessary fields can provide a measurable performance increase.

Just as with Redis’ optimized patterns for structures like hashes and sets, Firebase’s massive horizontal scalability is one of its key features. It shouldn’t just be a nice-to-have. It should be leveraged itself as a tool in your app designs.

<h4><strong>Avoid arrays</strong></h4>
The Firebase documentation already covers this topic. It’s all correct. Avoid them.


<h4><strong>One size does not fit all</strong></h4>
Leverage Firebase’s strengths. Don’t try to make it do every job you have. Here are some things Firebase is not:

1. A search engine. It has a few basic operations like prefix-matching, but that’s it. Use ELK, Algolia, etc. if you need full-powered search.
<br/>
2. An API stack. Cloud Functions for Firebase looks very promising, but is still in Beta. If your app is anything more than a to-do list, plan how you’ll do server-side/trusted code execution.
<br/>
3. A reporting engine. You may still want to leverage a relational database when you need to slice/dice/filter/mutate/munge/etc your data.
<br/>
4. Self-hosted or fully usable offline. Offline functionality is provided via sync/persistence, but the Firebase cloud must be involved at first.

<h4><strong>Set vs. update</strong></h4>
There’s a big difference between SET and UPDATE operations. It affects what happens if a key doesn’t exist yet, particularly keys inside complex objects. Pay attention to it!

<h4><strong>FirebaseUI is awesome!</strong></h4>
Oh I should mention, there are FirebaseUI companion projects for a lot of platforms. Use them. They’re incredibly helpful for things like setting up a tableview to display a list of objects in a Firebase collection.

This library provides FUICollectionViewDataSource and FUITableViewDataSource classes (and their equivalents on Android) that make getting started on a mobile platform just a few lines of code.

When you’re ready for more sophistication and control, FirebaseUI is still useful because it also provides the lower-level data collection classes FUIArray and FUIIndexArray that let you drive things like tab headers and other oddball displays.
