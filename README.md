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
ways using Obvious JSON.  That gives you a starting point, but how do
you teach your computers to talk to each other using this convention?
I guess you'll need to come up with a subset of valid Obvious JSON
that you will use when it must be machine readable.

One such subset, along with rules for forming URLs and handling
various HTTP methods, is described by
https://github.com/EarthlingInteractive/PHPCMIPREST.
