---
layout: post
title: "Concurrent Connection H2"
excerpt: "How to handle "
tags: [h2, database, poll, concurrent, corrupted]
comments: true
---

While working in a project which is based on a H2 database, we experienced some strange behavior after checking the behavior of the database.

## Scenario

1. The application uses a H2 file and keeps connected while open.
2. In addition using a utility like [DBeaver](http://dbeaver.jkiss.org/) it's possible to connect to the same database and check the data.

## Problem

Problem is that two connection were made to the same file and the jdbc driver couldn't rollback some transaction because the other 
connected instance locked the file... and boom corrupted state.

## Solution

H2 has a quite clever way to solve this.

> AUTO_SERVER=TRUE

```
file:/path/to/my/file/h2db_journal.db;MVCC=TRUE;DB_CLOSE_ON_EXIT=FALSE;IGNORECASE=TRUE;AUTO_SERVER=TRUE;
```

All instances trying to connect should use the AUTO_SERVER in order to connect to a server instead of straight to the .db file, 
this way the concurrency is orchestrated by this server.

Cheers.
