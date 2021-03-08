---
title: 'Getting your head around the Couchbase SyncGateway'
date: '2014-05-12T09:00:45+10:00'
permalink: /getting-your-head-around-the-couchbase-syncgateway
author: 'James Elsey'
category:
    - 'Big Data'
    - Couchbase
    - Databases
tag:
    - android
    - couchbase
    - ios
    - mobile
---
I like Couchbase. One of the things that really appeals to me is the sync gateway. As a mobile developer I often find that the apps I’m developing are just interfaces into some backend service. Somewhere out there in the cloud I’ll have a web application that sits on top of some database (nodejs/mongoDB is a combo I’ve been using recently). Then there comes the mobile app, which will be consuming these services, which would be fine if 4G/wifi was everywhere (I can’t even get a cellular signal at my place, let alone dream of 4G).

We’re into the realms of apps working offline, you then have the pain of syncing data and dealing with conflicts. You can make your life easier by using a [SyncAdapter](http://developer.android.com/training/sync-adapters/creating-sync-adapter.html) on Android, or perhaps a framework like [Restkit](http://restkit.org/) if you’re developing on iOS, heck, you can even implement the syncing yourself (don’t do that, that road leads to madness..speaking from experience)…OR…you can just use Couchbase and the SyncGateway.

In short, the SyncGateway is an application that sits between your Couchbase server, and your Couchbase Lite enabled mobile apps. This means you can access your data on your local CBLite database, and not have to worry (too much) about syncing this to the Couchbase server.

Getting setup
-------------

I have to admit, the documentation is a little confusing when it comes to explaining how the components hang together, but after attending [Couchbase Live in London](http://www.couchbase.com/couchbase-london-2014) a month or so back I was able to track down those who are in the know, and put the missing piece into my puzzle of confusion; bucket syncing.

For the purpose of explaining how this works, I’ll use my “Coin Collector” android app as the example. The app needs to get its data on coins from a couchbase server. It should be able to work offline and sync periodically. I’m using bucket syncing so I can have a web page to administer coins such as adding new coins to altering market values.  
The documentation is really missing a diagram like the following

![couchbase-sync](/assets/post_images/2014/couchbase-sync.jpg)

Let me cover the 4 points in blue numbers:

1. Regardless of which mobile platform you’re using, it’ll be connecting to the sync gateway via the REST apis, this is where “json over the wire” comes into play.
2. As the mobile apps use their own bucket, you need to configure the gateway to tell it where to put documents. If you check my config below; then this is done by the “aussie-coins-syncgw” configuration element, you can see that the bucket is set to “aussie-coins-bucket-sync-db” on the localhost couchbase server (sync and db are running on my local vm)
3. This is where the magic happens. Bucket shadowing in the later releases of the Sync Gateway allow it to sync changes between your “mobile” bucket, and your “backend” bucket. You can see this configured by the “shadow” element in my config.json
4. Your backend server apps can just connect to the “aussie-coins-bucket” and be totally oblivious to what is happening in the mobile side of your architecture.

```json
{
    "interface": ":4984",
    "adminInterface": ":4985",
    "log": ["CRUD", "CRUD+", "HTTP", "HTTP+", "Access", "Cache", "Shadow", "Shadow+", "Changes", "Changes+"],
    "databases": {
        "aussie-coins-syncgw": {
            "server": "http://localhost:8091",
            "bucket": "aussie-coins-bucket-sync-db",
            "sync": `function(doc) {channel(doc.channels);}`,
            "users": {
                "GUEST": {
                    "disabled": false,
                    "admin_channels": ["*"]
                }
            },
            "shadow": {
                 "server": "http://localhost:8091",
                 "bucket": "aussie-coins-bucket"
            }
        }
    }
}
```

Some other points to notice in the configuration:

- The interface port is the port the apps will connect on, the adminInterface is for administering the sync gateway, such as dynamically adding new databases, or altering channels.
- Logs, I’ve chosen to log everything, you can restrict these if you need, check the Couchbase documentation for further info.
- I’ve enabled the guest user access on all channels for the purpose of evaluating this, ideally we’d need to restrict the channels that users can use to stop any potential abuse.

Testing it out
--------------

As I mentioned above, since the mobile apps will be connecting to the Sync Gateway via a REST api, we can take the mobile app out of the picture and test using a rest client (I’m using Postman for Google Chrome). Lets cover 2 scenarios.

### Server Producing

This scenario involves a new document being created on the server, and it being synced to the mobile bucket and available to view on the mobile apps.

Firstly, let me show you what I have in the “aussie-coins-bucket”.

![1-couchbase-coins_bucket](/assets/post_images/2014/Screen-Shot-2014-05-09-at-22.49.07.png)

Next, lets create a new document with an ID of 5, for the Ten Cent coin. We should then see it listed in our “aussie-coins-bucket” like so:

![2-couchbase-coins_bucket_new_coin](/assets/post_images/2014/Screen-Shot-2014-05-09-at-22.50.04.png)

Now lets have a look at the log output from the Sync Gateway.

```
<pre class="brush: plain; title: ; notranslate" title="">
22:49:33.826838 Shadow+: Pulling "5", CAS=1e2dd7153a ... have UpstreamRev="", UpstreamCAS=0
22:49:33.826894 Shadow: Pulling "5", CAS=1e2dd7153a --> rev "1-1d7a1a352c0abb293fdd16883ef6985b"
22:49:33.826909 CRUD+: Invoking sync on doc "5" rev 1-1d7a1a352c0abb293fdd16883ef6985b
22:49:33.903707 Cache: SAVING #8
22:49:33.903984 CRUD: Stored doc "5" / "1-1d7a1a352c0abb293fdd16883ef6985b"
22:49:34.768280 Cache: Received #8 after 864ms ("5" / "1-1d7a1a352c0abb293fdd16883ef6985b")
22:49:34.768305 Cache:     #8 ==> channel "*"
22:49:34.768322 Changes+: Notifying that "aussie-coins-bucket-sync-db" changed (keys="{*}") count=3
22:49:59.849578 Shadow+: Pulling "5", CAS=2423d4b93a ... have UpstreamRev="1-1d7a1a352c0abb293fdd16883ef6985b", UpstreamCAS=c21019dd68
22:49:59.849623 Shadow: Pulling "5", CAS=2423d4b93a --> rev "2-971b4b3009127da5ed2a4770cb45cfe7"
22:49:59.849637 CRUD+: Invoking sync on doc "5" rev 2-971b4b3009127da5ed2a4770cb45cfe7
22:49:59.849749 CRUD+: Saving old revision "5" / "1-1d7a1a352c0abb293fdd16883ef6985b" (68 bytes)
22:49:59.849891 CRUD+: Backed up obsolete rev "5"/"1-1d7a1a352c0abb293fdd16883ef6985b"
22:49:59.850068 Cache: SAVING #9
22:49:59.850207 CRUD: Stored doc "5" / "2-971b4b3009127da5ed2a4770cb45cfe7"
22:50:00.790818 Cache: Received #9 after 940ms ("5" / "2-971b4b3009127da5ed2a4770cb45cfe7")
22:50:00.790838 Cache:     #9 ==> channel "*"
22:50:00.790868 Changes+: Notifying that "aussie-coins-bucket-sync-db" changed (keys="{*}") count=4
```

As we can see, the Sync Gateway has detected that there is a new document and that it needs to shadow it across, which is does successfully.

On the couchbase server, we can view that document in the mobile bucket, “aussie-coins-sync-db” like so:

![3-couchbase_synced_coin](/assets/post_images/2014/Screen-Shot-2014-05-09-at-22.51.14.png)

Finally, just to prove the mobile clients can see that document via the API, do a GET on http://localhost:4984/aussie-coins-syncgw/5 and you’ll see the following:

```json
{
    "_id": "5",
    "_rev": "2-971b4b3009127da5ed2a4770cb45cfe7",
    "coin": "Ten Cent"
}
```

### Mobile Producer

Now we’ll try the opposite, producing documents from the mobile clients and seeing them synced across to the Couchbase server. From a REST client, do a PUT to http://localhost:4984/aussie-coins-syncgw/6 with a json body of:

```json
{
  "coin":"Twenty Cent"
}
```

You should see a response of

```json
{
    "id": "6",
    "ok": true,
    "rev": "1-e9c16d3887a3958314adff1e3cbd6097"
}
```

What we’ve done is to create a document with the ID of 6, for “Twenty Cent”.

Lets have a look at the Sync Gateway logs:

```
<pre class="brush: plain; title: ; notranslate" title="">
23:19:56.860618 HTTP:  #003: PUT /aussie-coins-syncgw/6
23:19:56.971056 CRUD+: Invoking sync on doc "6" rev 1-e9c16d3887a3958314adff1e3cbd6097
23:19:57.023839 Cache: SAVING #10
23:19:57.024110 CRUD: Stored doc "6" / "1-e9c16d3887a3958314adff1e3cbd6097"
23:19:57.024161 HTTP+: #003:     --> 201   (0.0 ms)
23:19:57.616316 Cache: Received #10 after 592ms ("6" / "1-e9c16d3887a3958314adff1e3cbd6097")
23:19:57.616340 Cache:     #10 ==> channel "*"
23:19:57.616353 Shadow: Pushing "6", rev "1-e9c16d3887a3958314adff1e3cbd6097"
23:19:57.616367 Changes+: Notifying that "aussie-coins-bucket-sync-db" changed (keys="{*}") count=6
23:19:57.852304 Shadow+: Pulling "6", CAS=1c6f07c3ce2 ... have UpstreamRev="", UpstreamCAS=0
23:19:57.852327 Shadow+: Not pulling "6", CAS=1c6f07c3ce2 (echo of rev "1-e9c16d3887a3958314adff1e3cbd6097")
23:19:57.852337 CRUD+: Invoking sync on doc "6" rev 1-e9c16d3887a3958314adff1e3cbd6097
23:19:57.865669 CRUD+: updateDoc("6"): Rev "1-e9c16d3887a3958314adff1e3cbd6097" leaves "1-e9c16d3887a3958314adff1e3cbd6097" still current
23:19:57.865751 Cache: SAVING #11
23:19:57.866050 CRUD: Stored doc "6" / "1-e9c16d3887a3958314adff1e3cbd6097"
23:19:58.617446 Cache: Received #11 after 751ms ("6" / "1-e9c16d3887a3958314adff1e3cbd6097")
23:19:58.617463 Cache:     #11 ==> channel "*"
23:19:58.617482 Changes+: Notifying that "aussie-coins-bucket-sync-db" changed (keys="{*}") count=7
```

We can then see the document in the “aussie-coins-bucket-sync-db”:

![4-couchbase_syncdb](/assets/post_images/2014/Screen-Shot-2014-05-09-at-23.22.27.png)

…and then in the “aussie-coins-bucket”:

The Sync Gateway is a useful application, and really does make the Couchbase offering even more appealing. Once you can get your head around the bucket shadowing (which you should if you’ve made it this far) then it can be easy to work with.

Comment or find me on Twitter (@jameselsey1986) if you have any questions!

Why the duplication?
--------------------

Having 2 buckets for the same data had me raise an eyebrow initially, but after [asking on Google Groups](https://groups.google.com/d/msg/mobile-couchbase/WY_qS-_ZjCg/twpVGJ21SzUJ), it does make sense. You can’t expect the backend app servers to maintain sync meta data on new documents it creates. Perhaps Couchbase will alter this in the future.

Resources
---------

- [Sync Gateway Documentation](http://docs.couchbase.com/sync-gateway/)
- [Sync Gateway](https://github.com/couchbase/sync_gateway) source on Github
- [Google groups Couchbase Mobile](https://groups.google.com/forum/#!forum/mobile-couchbase)