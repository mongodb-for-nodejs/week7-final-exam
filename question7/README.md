# Question 7

You have been tasked to cleanup a photosharing database. The database consists of two collections, albums, and images. Every image is supposed to be in an album, but there are orphan images that appear in no album. Here are some example documents (not from the collections you will be downloading).


````
> db.albums.findOne()
{
    "_id" : 67
    "images" : [
        4745,
        7651,
        15247,
        17517,
        17853,
        20529,
        22640,
        27299,
        27997,
        32930,
        35591,
        48969,
        52901,
        57320,
        96342,
        99705
    ]
}

> db.images.findOne()
{ "_id" : 99705, "height" : 480, "width" : 640, "tags" : [ "dogs", "kittens", "work" ] }
````

From the above, you can conclude that the image with _id = 99705 is in album 67. It is not an orphan.

Your task is to write a program to remove every image from the images collection that appears in no album. Or put another way, if an image does not appear in at least one album, it's an orphan and should be removed from the images collection.

Download and unzip Final7.zip and use mongoimport to import the collections in albums.json and images.json.

When you are done removing the orphan images from the collection, there should be 89,737 documents in the images collection. To prove you did it correctly, what are the total number of images with the tag 'kittens" after the removal of orphans? As as a sanity check, there are 49,932 images that are tagged 'kittens' before you remove the images.

Hint: you might consider creating an index or two or your program will take a long time to run.

* 49,932
* 47,678
* 38,934
* 45,911
* 44,822

### Answer

Import both collections

````
mongoimport -d gallery -c albums < albums.json
````

````
mongoimport -d gallery -c images < images.json
````

Count kittens

````
db.images.count({
    tags: { $in: ['kittens'] }
})
````

Result: 49932


Remove all orphans

````
db.albums.ensureIndex({
    images: 1
});

db.images.find().forEach(function (image) {
    if (db.albums.count({images: { $in: [image._id]} }) == 0) {
        db.images.remove({ _id: image._id });
    }
})
````

Count kittens again

````
db.images.count({
    tags: { $in: ['kittens'] }
})
````

Result: 44822

