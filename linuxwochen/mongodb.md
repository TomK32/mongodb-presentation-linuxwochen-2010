!SLIDE cover
# MongoDB #

### *Thomas R. Koll* ###
### ananasblau.com ###
#### 2010-05-06 ####
#### http://tomk32.github.com/mongodb-presentation-linuxwochen-2010/ ####

!SLIDE
# MongoDB ist eine einfach skalierbare, schnelle, verteilte und dokumentbasierte Datenbank ohne festem Schema. #

!SLIDE
# MongoDB ist keine relationale Datenbank und kann auch keine Transkationen. #

!SLIDE bullets
# Hard facts #

* Besteht seit 10/2007, open source seit 2009
* Entwickelt von 10gen
* Aktuell 1.4.2 / 1.5.1 (unstable)

!SLIDE bullets
# Glossar für SQL Programmierer #

* database => database
* table => collection
* row => document
* column => field

!SLIDE bullets
# Was kann man damit machen? #

* Dynamische Webapplikationen
* Statistiken führen
* Dokumente können unterschiedliche Felder haben
* Dokumente können in anderen Dokumenten stecken
* Natürlicher in einer objekt-orientierten Umgebung

!SLIDE bullets
# Noch mehr #

* Sehr einfach über mehrere Server zu skalieren
 * Replication (verteiltes Lesen)
 * Sharding (verteiltes Schreiben)

!SLIDE bullets
# Und noch mehr

* Atomare Operationen ($push, $set, $inc)
* Upserts (also update/insert)
* map/reduce (Verteilte Zwischenverarbeitung)
* Capped Collections
* GridFS

!SLIDE bullets smallesr
# Datentypen #
## string, int, double, boolean, date, bytearray, **object**, **array** ##

!SLIDE execute smaller
# Let's store some data #

    @@@ javascript
    $ mongo
    > use blog
    > db.posts.insert({
      title: 'First post',
      author: 'TomK32',
      tags: ['metalab', 'hacking', 'mongodb'],
      body: 'This is my first blogpost.',
      published_at: new Date() })

!SLIDE execute smaller
# Let's do a few finds #

    @@@ javascript
    // show all posts
    > db.posts.find()

    // Find by author
    > db.posts.find({ author: 'TomK32' })
      {
        "_id" : ObjectId("4be28c83ab28e8473a69687f"),
        "title" : "First post",
        "author" : "TomK32",
        "tags" : [ "metlab", "hacking", "mongodb"],
        "body" : "This is my first blogpost.",
        "published_at" : "Wed Mar 31 2010 20:31:44 GMT+0200 (CEST)"
      }

!SLIDE execute smaller
# More awesome queries #

    @@@ javascript
    // Find by value inside an Array, like tags
    > db.posts.find({ tags: 'metalab' })

    // not by TomK32 but tagged 'metalab'
    > db.posts.find({ author: { $ne: 'TomK32' }, tags: 'metalab' })

    // has no tags
    > db.posts.find({tags: {$exists: false}})

    // no future posts
    > db.posts.find({published_at: {$lt: new Date()}})

!SLIDE execute smaller
# Let's embed a comment #
    @@@ javascript
    > db.posts.update({_id: ObjectId('4be28c83ab28e8473a69687f')},
        { $push: { comments: { author: 'Sammy', body: 'Buy V1agra!!elf!' }}})

    // Only get the comments
    > db.posts.find({}, {comments:1})
      {
        "_id" : ObjectId("4be28c83ab28e8473a69687f"),
        "comments" : [
          {
            "author" : "Sammy",
            "body" : "Buy V1agra!!elf!"
          }
        ]
      }

!SLIDE execute smaller
# Find based on embedded document #
    @@@ javascript
    > db.posts.find({'comments.author': 'Sammy' }, {comments: 1})

    // Upsert
    > db.posts.update( {'comments.author': 'Sammy'},
        {$inc:{'comments.$.votes':1}}, false, true )
      {
        "_id" : ObjectId("4be28c83ab28e8473a69687f"),
        "comments" : [
          {
            "author" : "Sammy",
            "body" : "Buy V1agra!!elf!",
            "votes" : 1
         }
       ]
     }

    // Regexp!
    > db.posts.find({'comments.body': /Buy/ })

!SLIDE bullets
# Ein Datenbank Model am Beispiel photostre.am #

* 3 collections (statt 10 im MySQL) und 8 Models
* User mit einem Array von Sources
* Photo referenziert Source und User, hat Array von tags
* Website mit Arrays: Pages, Albums, hat Array von user_ids
* Album mit einem Array von Photos

!SLIDE bullets smaller
# Alternativen #

* CouchDB
* Cassandra (Facebook, Twitter)
* XML Datenbanken

!SLIDE bullets

* http://mongodb.org
* http://ananasblau.com
