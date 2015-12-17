# Obvious JSON

A convention for encoding objects and 'doing REST services' using JSON in a somewhat obvious way.

## Obviousness

If you asked someone who recently learned about JSON and was familiar
with basic programming concepts (and who is used to using camelCase
for attribute names) to encode information about a thingamabob with related
doohickeys, each of which had a furb and a durp as JSON, they'd
probably come up with something like the following:

```
{
  "relatedDoohickeys": [
    {
      "furb": {
        "name": "Waka waka!"
      },
      "durp": "Durps are just strings.  Doesn't everyone know that?"
    },
    {
      "furb": {
        "name": "Whoosh whoosh!"
      },
      "durp": "This is the durp of a different doohickey!"
    }
  ]
}
```

Each object is represented as a JSON object.  Sets of objects are
represented as JSON arrays, full of JSON objects.

That's valid Obvious JSON!

But there are other things you might want to do.  Maybe furbs are very
large complicated things and we don't want to encode furbs directly
into the JSON representatino of the object containing them, but we do
want to reference them, somehow.  Well, the obvious thing to do is to
include some kind reference to the furb, maybe a 'furb ID'.  Well,
there's an obvious way to do that, too.

```
{
  "furbId": "1234"
}
```

That's also valid Obvious JSON!

Sometimes you want to indicate the type of something along with
whatever other data is associated.  Class can be thought of as just
another attribute, and while we might not want to encode an object
representing the class itself, we can certainly encode its name.

```
{
  "className": "Thingamabob",
  "relatedDoohickeys": [
    {
      "className": "Doohickey",
      "furbId": "1234",
      "durp": "An extra special durp up in here."
    }
  ]
}
```

Oh wow, the fact that objects belong to some class can also be
represented in Obvious JSON!

## Oh, the flexibility!

So you see, you can represent a lot of things in a lot of different
ways using Obvious JSON.

One such subset, along with rules for forming URLs and handling
various HTTP methods, is described by
https://github.com/EarthlingInteractive/PHPCMIPREST.

# FAQ

### How do I reference an object by ID?

There are a couple of ways.

The most straightforward is to simply have a field on the referencing
object that contains the ID of the referenced object, just like you
would if you were referencing a record in a relational database from
another record.  You probably want to call this field something that
indicates the relationship of the referenced object and the fact that
this field is an ID, e.g. if a person owns a hat, you could have your
JSON-encoded person object have a 'hatId'.

### Should all objects have an 'id' field?

That's up to you.  There are cases where it makes sense for objects to
know their own ID, and cases where it does not.

### Do I have to use camelCase?

You don't have to, but I recommend it.  It seems kind of a standard
both for JavaScript and for things like RDF (where predicate names
tend to be camelCase).  Users of your API will be less surprised and
it's more likely that this will match the way they name fields when
interfacing with other APIs.

### Sometimes I want to reference an object by ID and sometimes include it inline.  How do I design my API around this?

I would say that if the referenced object always has an ID, then
always include the "referencedObjectId" so that users of your API can
rely on it, and include the object inline when it makes sense to do
so.

Otherwise, the fact that users sometimes have to look for
"someObjectId" and sometimes for "someObject" should just be part of
your API.

### Would I ever reference objects by something other than an 'ID' field?

If your IDs are always URIs, for example, consider calling the field
name "somethingUri".

Composite keys are fine, too.  One benefit of Obvious JSON is that
your objects map very directly to relational database records.

### What if I want to reference a list of objects?

Then have a list of IDs and call it "somethingSomethingIds".

### How do I represent symmetrical many-to-many relationships?

The simplest way is to use link records.  e.g. a person-hat link record:

```
{
	"personId": 123,
	"hatId": 457
}
```

This avoids confusion and API complexity about 'how do I manipulate links?'.
You just add and remove link records.
And again, this makes mappings to relational database tables very straightforward.

Of course, if you really want to, you can use ID lists to encode
relationships between the objects.

e.g. example person record:

```
{
	"id": 123,
	"name": "Bob",
	"hatIds": [ 457, 873 ]
}
```

And an example hat record:

```
{
	"id": 457,
	"name": "Bob's favorite hat that he also shares with Jim",
	"personIds": [ 123, 234 ]
}
```

If you take this approach, I recommend treating the ID lists as
read-only and only allowing manipulation of links via the person-hat
service.

### What about non-symmetrical many-to-many relationships?

If one object is always the referencer and one the referencee, that
makes things a bit more obvious.  In that case it is reasonable to
treat the referencer as having a list of referencee IDs and allow that
list to be modified by PUT/POST/PATCH requests to the referencer.
