# Understanding Linked Data

What does the concept of Linked Data mean to you as a developer? It means that
you have datasets that have the following properties:

 1. Globally unique IDs (since they use URIs for IDs). Also, you can almost
    always dereference those IDs and get more detailed useful data from them.
 2. Globally unique, collision free, *reusable* property names (or column names,
    if you're coming from an RDBMS world).
 3. The datasets are self-documenting and self-describing. If you dereference
    each of the unique property names, you get comments, context, data types,
    and if you're lucky, validatable schemas.

The easiest way to understand the benefits and challenges of Linked Data is to
start with something familiar to most developers -- data in the CSV format.
Let's say we want to store some user records:

```csv
id,name,birth_date
1,"Alice","1990-01-01"
2,"Bob","1995-02-02"
3,"Cindy","1999-01-01"
```

We have data, we have property names on the first line, but there are several
challenges here. For one, although the meanings of the example property names
are fairly easy to guess, anybody who's worked with CSV datasets knows that this
is not always the case. It would be of immense help to be able to have some sort
of explanations or comments alongside that first line, to understand what the
properties are and how to process them. Along the same lines, what the *schema*
of the properties is far from clear (such as their data types and validation
logic). Lastly, this dataset is not very *portable*, in terms of its IDs.
They appear to be the usual sort of auto-incrementing integer, but it's not easy
to simply merge them into an existing dataset (say, a Users table), since those
IDs could already be taken up by existing users. To put it another way, those
IDs are not very collision-resistant.

Let's put that same dataset into JSON format, to make it slightly easier for
developers to understand and reference. (CSV, among other drawbacks, does not
enable you to express nested data structures very easily.)

```json
[
  { "id": 1, "name": "Alice", "birth_date": "1990-01-01" },
  { "id": 2, "name": "Bob", "birth_date": "1995-02-02" },
  { "id": 3, "name": "Cindy", "birth_date": "1999-01-01" }
]
```

A little better; we can now refer to a property from a parsed row by name (say,
`user.name`) instead of by index (`user[1]`).

Now, imagine if we could give each of those users a *globally unique* `id`.
Maybe each of them have their own domain name. Or failing that, an account on
some service provider. Then we would have:

```json
[
  { "id": "http://www.alice.com", "name": "Alice", "birth_date": "1990-01-01" },
  { "id": "http://bob.provider.com", "name": "Bob", "birth_date": "1995-02-02" },
  { "id": "http://cindy.provider.com", "name": "Cindy", "birth_date": "1999-01-01" }
]
```

Now, all of a sudden, the dataset becomes much more portable. We can add it
into existing datasets with no fear of id collisions. And if a record with the
id of `http://www.alice.com` already exists, we can merge it and fill in missing
properties.

The property names are still a bit ambiguous though. Does `name` mean full name,
or just the given name? We *could* do the same thing with property names as we
did with the IDs, and just use URIs:

```json
[
  {
    "id": "http://www.alice.com",
    "http://schema.org/givenName": "Alice",
    "http://schema.org/birthDate": "1990-01-01"
  },
  {
    "id": "http://bob.provider.com",
    "http://schema.org/givenName": "Bob",
    "http://schema.org/birthDate": "1995-02-02"
  },
  {
    "id": "http://cindy.provider.com",
    "http://schema.org/givenName": "Cindy",
    "http://schema.org/birthDate": "1999-01-01"
  }
]
```

Now, all of a sudden, we have reusable, unambiguous properties. With the added
benefit of -- we can *resolve* those properties as HTTP URIs and get a
human-readable comment explaining its semantics, *and* the data format and
validation constraints for the values (for example, the fact that the
`birthDate` is in ISO 8601 date format). And they're reusable in the sense of,
now app developers are encouraged to simply use `http://schema.org/birthDate`
for the birth date property name, instead of various incompatible combinations
of `birthdate`, `birth_date`, `bd`, and so on.

Of course, repeating the full URL for the property name for each record gets
a little verbose, and not very DRY. Let's factor out the part of the URL that
they have in common, and put them in a lookup dictionary, in its own `context`
section.

```json
{
  "context": {
    "givenName": "http://schema.org/givenName",
    "birthDate": "http://schema.org/birthDate"
  },
  "data": [
    {
      "id": "http://www.alice.com",
      "givenName": "Alice",
      "birthDate": "1990-01-01"
    },
    {
      "id": "http://bob.provider.com",
      "givenName": "Bob",
      "birthDate": "1995-02-02"
    },
    {
      "id": "http://cindy.provider.com",
      "givenName": "Cindy",
      "birthDate": "1999-01-01"
    }
  ]
}
```

And now we have the best of both worlds. We have compact property names (so
that we  can once again refer to a parsed user's property as `user.givenName`
instead of something horrible like `user['http://schema.org/givenName']`). And
we still retain the benefit of globally unique unambiguous dereferenceable
property names (they just get short, readable local aliases).

Congratulations, we have just arrived at a decent Linked Data document. And
with a few tweaks (we'll use the reserved properties `@id` and `@context`, and
`@graph` instead of `data`), we now have full-fledged RDF in the JSON-LD
serialization format:

```json
{
  "@context": {
    "givenName": "http://schema.org/givenName",
    "birthDate": "http://schema.org/birthDate"
  },
  "@graph": [
    {
      "@id": "http://www.alice.com",
      "givenName": "Alice",
      "birthDate": "1990-01-01"
    },
    {
      "@id": "http://bob.provider.com",
      "givenName": "Bob",
      "birthDate": "1995-02-02"
    },
    {
      "@id": "http://cindy.provider.com",
      "givenName": "Cindy",
      "birthDate": "1999-01-01"
    }
  ]
}
```

You can now parse it using RDF parsing libraries, transform it into RDF-XML or
HTML-based RDFa formats, use schema validation tools, and so on.
