#    CVE-Search Changelog
##   What's new in CVE-Search v2?

 * Plug-in manager
   * Seen, Bookmarks and MISP have been moved to plug-ins. See [our plug-in repo](https://github.com/cve-search/Plugins)
 * Bugfixes
 * Master pages
 * Working towards Windows compatibility
 * Internal working restructuring
   * Database abstraction
   * Database searching
   * Function streamlining
 * CWE browser
 * User requests
   * Posibility to view last update log in web interface
   * Minimal update (only CVE and CPE)

The update from CVE-Search v1.x to v2 will require database modifications if you wish to keep user data. The database does not have to be re-imported, but collections have to be moved around. Below, you will find the steps to:

###  Preserve data
In order to preserve your data, you should take the folowing steps, for the folowing plug-ins:

#### Seen
To move the userdata from `db.mgmt_seen` to the Seen plug-in collection (`db.plug_user_seen`), log in to the mongo console and execute the folowing code:

```
db.mgmt_seen.find().forEach(function(doc){
  doc["cves"] = doc["seen_cves"];
  db.plug_user_seen.insert(doc);
  db.plug_user_seen.update(doc, {'$unset': { 'seen_cves': "", 'bookmarked': ""}})
});
```

#### Bookmarks
To move the userdata from `db.mgmt_seen` to the Bookmarks plug-in collection (`db.plug_user_bookmarks`), log in to the mongo console and execute the folowing code:

```
db.mgmt_seen.find().forEach(function(doc){
  doc["bookmarks"] = doc["bookmarked"];
  db.plug_user_bookmarks.insert(doc);
  db.plug_user_bookmarks.update(doc, {'$unset': { 'seen_cves': "", 'bookmarked': ""}})
});
```

#### MISP
We would recommend to re-initiate your import, especially since the plug-in automatically saves the last update, also in a different location. If you don't want to do this, you can move the collection with the folowing code:

```
db.user_misp.copyTo("plug_info_misp")
db.info.find({"db": "user_misp"}).forEach(function(doc){
  db.plugin_settings.insert({"plugin": "user_misp", "last_update": doc["last-modified"]});
});
```

###  Clean the database
Regardless of wether you want to use the plug-ins, you can clean your database by removing the un-used collections and entries.
We will not differentiate between the separate plug-ins, since this is irrelevant.

```
db.mgmt_seen.drop()
db.user_misp.drop()
db.info.remove({"db": "user_misp"})

```