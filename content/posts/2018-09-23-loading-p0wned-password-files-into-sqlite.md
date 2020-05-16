---
categories:
- Everyday
date: "2018-09-23T12:58:34Z"
meta: null
status: publish
tags: []
title: Loading "P0wned" password file into SQLite
---
<!-- wp:paragraph -->

Recently I had a discussion with a friend of mine with regards to checking AD passwords against the "P0wned password list" released by Troy Hunt.

<!-- /wp:paragraph -->

<!-- wp:paragraph -->

So I went and downloaded the the file from [here](https://haveibeenpwned.com/Passwords)

<!-- /wp:paragraph -->

<!-- wp:paragraph -->

That turned out to be a 8.8 GB Zip file, that unpacked to almost 19GB of raw text. (The file consists of to columns of data the NTHash and the number of occurrences across password leaks.)

<!-- /wp:paragraph -->

<!-- wp:paragraph -->

Having to look through 19 GB of unsorted data I knew would be very slow, but I decided to test out different approaches. Keeping in mind, that I wanted to integrate this with my previously released AD Password checker tool [<g class="gr_ gr_224 gr-alert gr_gramm gr_inline_cards gr_run_anim Style multiReplace" id="224" data-gr-id="224">here</g>](https://github.com/Claustn/Password-Report)<g class="gr_ gr_224 gr-alert gr_gramm gr_inline_cards gr_disable_anim_appear Style multiReplace" id="224" data-gr-id="224"> ,</g> and the test data I have in AD consists of about 500 Users with the last 20 passwords stored in HashHistory, meaning that I would have to do about 10.000 lookups into the file.

<!-- /wp:paragraph -->

<!-- wp:separator -->

* * *
<!-- /wp:separator -->

<!-- wp:paragraph -->

First attempt was simply to use Select-String and look for <g class="gr_ gr_5 gr-alert gr_gramm gr_inline_cards gr_run_anim Grammar multiReplace" id="5" data-gr-id="5">a NTHash</g> value, even on a very fast Nvme drive this took more than 2 minutes per query, meaning it would take 20.000 minutes + (14 days)

<!-- /wp:paragraph -->

<!-- wp:separator -->

* * *
<!-- /wp:separator -->

<!-- wp:paragraph -->

I then tried Grep and Sift which yielded results in the same 2 minute area as Select-String.

<!-- /wp:paragraph -->

<!-- wp:separator -->

* * *
<!-- /wp:separator -->

<!-- wp:paragraph -->

Since I already had all the AD data in <g class="gr_ gr_4 gr-alert gr_gramm gr_inline_cards gr_run_anim Grammar multiReplace" id="4" data-gr-id="4">a SQLite</g> database, I thought I would try to load the data into the DB<g class="gr_ gr_5 gr-alert gr_gramm gr_inline_cards gr_run_anim Punctuation multiReplace" id="5" data-gr-id="5">.</g>&nbsp;So I loaded the data into the DB and tried to query the DB for a specific Hash, this was a little bit faster than the plain file, but still took 1.40 minutes. Then I considered adding an index to the NTHash column, but since that has almost 520 million rows, and takes up 99% of the space used, it would mean that the DB would be double the size (around 40GB), which I felt would make it less portable.

<!-- /wp:paragraph -->

<!-- wp:separator -->

* * *
<!-- /wp:separator -->

<!-- wp:paragraph -->

I really wanted to be able to keep everything in a way, so this could be run without "installing" anything and keeping it portable. So loading the database into MySQL really was not an option either. So I started reading the SQLite documentation and realized that it supports&nbsp; Clustered indexed. Meaning that I could omit the default index and have SQLite use the NTHash data as an index. This means that the import time into the DB will go up (Takes about 3½ -4 hours),&nbsp; because the data has to be sorted in order for it to act as an index. Since this data is very static the cost of using NTHash as an index is very low. With the data indexed the query time has gone done to about 10ms, which is very acceptable.

<!-- /wp:paragraph -->

<!-- wp:paragraph -->

<!-- /wp:paragraph -->

<!-- wp:separator {"className":"is-style-wide"} -->

* * *
<!-- /wp:separator -->

<!-- wp:heading -->

## Import Data

<!-- /wp:heading -->

<!-- wp:paragraph -->

First off we need to create the <g class="gr_ gr_63 gr-alert gr_gramm gr_inline_cards gr_run_anim Punctuation only-del replaceWithoutSep" id="63" data-gr-id="63">table,</g> and tell SQLite not to use it "regular" index,&nbsp; by telling it not to add a RowID, but use a "clustered index".

<!-- /wp:paragraph -->

<!-- wp:paragraph -->

<!-- /wp:paragraph -->

<!-- wp:code -->

```
CREATE TABLE p0wned (
	NTHash	TEXT,
	cnt	INTEGER,
	PRIMARY KEY(NTHash)
) WITHOUT ROWID;
```

<!-- /wp:code -->

<!-- wp:paragraph -->

Then we need to set the right import settings.

<!-- /wp:paragraph -->

<!-- wp:code -->

```
.mode csv
.separator :
.import "C:/temp/dbtest/pwds.txt" p0wned
```

<!-- /wp:code -->

<!-- wp:paragraph -->

This can of course also be automated, we can create a sql.txt file and put this into it.

<!-- /wp:paragraph -->

<!-- wp:code -->

```
CREATE TABLE p0wned (
	NTHash	TEXT,
	cnt	INTEGER,
	PRIMARY KEY(NTHash)
) WITHOUT ROWID;
.mode csv
.separator :
.import "C:/temp/dbtest/pwds.txt" p0wned
```

<!-- /wp:code -->

<!-- wp:paragraph -->

And run (This does require the sqlite3 binary)

<!-- /wp:paragraph -->

<!-- wp:code -->

```
Get-Content .\sql.txt | & sqlite3 test1.db
```

<!-- /wp:code -->

<!-- wp:paragraph -->

This has given us an ultra fast way to lookup data in the "Have I been p0wned" database, and all can be run locally.

<!-- /wp:paragraph -->

<!-- wp:paragraph -->

Next up I will show you how I have used that in my AD Password report script, to tell if users are using password that are in the "Have I been P0wned" list, or if you have users who are particular bad at passwords.

<!-- /wp:paragraph -->

