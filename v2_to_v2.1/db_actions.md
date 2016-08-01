If you wish to preserve the dates, open a mongo terminal,and execute the following command.
 (change `cvedb` to the appropriate database)

```
use cvedb

db.cves.find().forEach(function(doc){
  if(doc["Published"]){     doc["Published"]     = ISODate(doc["Published"]) }
  if(doc["Modified"]){      doc["Modified"]      = ISODate(doc["Modified"]) }
  if(doc["last-modified"]){ doc["last-modified"] = ISODate(doc["last-modified"]) }
  if(doc["cvss-time"]){     doc["cvss-time"]     = ISODate(doc["cvss-time"]) }
  db.cves.update({id: doc["id"]}, doc)
});

db.info.drop()
```
