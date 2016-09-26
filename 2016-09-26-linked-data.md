# Understanding Linked Data

What does the concept of Linked Data mean to you as a developer? It means that you have datasets that have the following properties:

 1. Globally unique IDs (since they use URIs for IDs). Also, you can almost always dereference those IDs and get more detailed useful data from them.
 2. Globally unique, collision free, *reusable* property names (or column names, if you're coming from an RDBMS world).
 3. The datasets are self-documenting and self-describing. If you dereference each of the unique property names, you get comments, context, data types, and if you're lucky, validatable schemas.

## Linked Data from First Principles

The easiest way to understand the benefits and challenges of Linked Data is to start with something familiar to most developers -- data in the CSV format. Let's say we want to store some user records:

```csv
id,name,birth_date
1,"Alice","1990-01-01"
2,"Bob","1995-02-02"
3,"Cindy","1999-01-01"
```

We have data, we have property names on the first line, but there are several challenges here. For one, although the meanings of the example property names are fairly easy to guess, anybody who's worked with CSV datasets knows that this is not always the case. It would be of immense help to be able to have some sort of explanations or comments alongside that first line, to understand what the properties are and how to process them. Along the same lines, the *schema* of the properties is far from clear (such as their data types and validation logic). Lastly, this dataset is not very *portable*, in terms of its IDs. They appear to be the usual sort of auto-incrementing integer, but it's not easy to add them to an existing dataset (say, a Users table), since those IDs could already be taken up by existing users. To put it another way, those IDs are not very collision-resistant.

Let's put that same dataset into JSON format, to make it slightly easier for developers to understand (and use in their code).

```json
[
  { "id": 1, "name": "Alice", "birth_date": "1990-01-01" },
  { "id": 2, "name": "Bob", "birth_date": "1995-02-02" },
  { "id": 3, "name": "Cindy", "birth_date": "1999-01-01" }
]
```

A little better -- we can now refer to a property from a parsed row by name (say, `user.name`) instead of by index (`user[1]`).

Now, imagine if we could give each of those users a *globally unique* `id`. Maybe each of them has their own domain name. Or failing that, an account on some service provider. Then we would have:

```json
[
  { "id": "http://www.alice.com#me", "name": "Alice", "birth_date": "1990-01-01" },
  { "id": "http://bob.provider.com#about", "name": "Bob", "birth_date": "1995-02-02" },
  { "id": "http://cindy.provider.com#about", "name": "Cindy", "birth_date": "1999-01-01" }
]
```

Now the dataset becomes much more portable. We can merge it into existing datasets with no fear of id collisions. Not only that, but now we can dereference those IDs and hopefully be able to get more useful data, such as a public user profile.

Incidentally, HTTP URIs is not the only way to have globally unique identifiers. Other schemes have been used, such as [XRI](https://en.wikipedia.org/wiki/Extensible_Resource_Identifier). The benefit of HTTP URIs should be obvious, however -- the tooling and infrastructure and developer familiarity with those is considerable.

The property names are still a bit ambiguous though. Does `name` mean full name, or just the given name? To address this, we could do the same thing with property names as we did with the IDs, and just use URIs:

```json
[
  {
    "id": "http://www.alice.com#me",
    "http://schema.org/givenName": "Alice",
    "http://schema.org/birthDate": "1990-01-01"
  },
  {
    "id": "http://bob.provider.com#about",
    "http://schema.org/givenName": "Bob",
    "http://schema.org/birthDate": "1995-02-02"
  },
  {
    "id": "http://cindy.provider.com#about",
    "http://schema.org/givenName": "Cindy",
    "http://schema.org/birthDate": "1999-01-01"
  }
]
```

Now, all of a sudden, we have reusable, unambiguous properties. With the added benefit of -- we can *resolve* those properties as HTTP URIs and get a human-readable comment explaining its semantics, *and* the data format and validation constraints for the values (for example, the fact that the `birthDate` is in ISO 8601 date format). And they're reusable in the sense of, now app developers are encouraged to simply use `http://schema.org/birthDate` for the birth date property name, instead of various incompatible combinations of `birthdate`, `birth_date`, `bd`, and so on.

Of course, repeating the full URL for the property name for each record gets a little verbose, and not very DRY. Let's factor out the property name URIs, and put them in a lookup dictionary, in their own `context` section.

```json
{
  "context": {
    "givenName": "http://schema.org/givenName",
    "birthDate": "http://schema.org/birthDate"
  },
  "data": [
    {
      "id": "http://www.alice.com#me",
      "givenName": "Alice",
      "birthDate": "1990-01-01"
    },
    {
      "id": "http://bob.provider.com#about",
      "givenName": "Bob",
      "birthDate": "1995-02-02"
    },
    {
      "id": "http://cindy.provider.com#about",
      "givenName": "Cindy",
      "birthDate": "1999-01-01"
    }
  ]
}
```

And now we have the best of both worlds. We have compact property names (so that we can once again refer to a parsed user's property as `user.givenName` instead of something horrible like `user['http://schema.org/givenName']`). And we still retain the benefit of globally unique unambiguous dereferenceable property names (they just get short, readable local aliases). (By the way, a thematic grouping of properties, such as `http://schema.org/`, is referred to as a *vocabulary* or *ontology* in the Linked Data community.)

Congratulations, we have just created a proper Linked Data document. And with a few tweaks (we'll use the reserved properties `@id` and `@context`, and `@graph` instead of `data`), we can turn this into full-fledged [RDF](https://en.wikipedia.org/wiki/Resource_Description_Framework) based linked data, using the [JSON-LD](https://en.wikipedia.org/wiki/JSON-LD) serialization format. (You can have Linked Data without using any of the RDF formats, it's just that by using them, you get access to a rich ecosystem of tools, standards, databases, validators, reasoners and deduction engines, a standardized query syntax, and so on.)

```json
{
  "@context": {
    "givenName": "http://schema.org/givenName",
    "birthDate": "http://schema.org/birthDate"
  },
  "@graph": [
    {
      "@id": "http://www.alice.com#me",
      "givenName": "Alice",
      "birthDate": "1990-01-01"
    },
    {
      "@id": "http://bob.provider.com#about",
      "givenName": "Bob",
      "birthDate": "1995-02-02"
    },
    {
      "@id": "http://cindy.provider.com#about",
      "givenName": "Cindy",
      "birthDate": "1999-01-01"
    }
  ]
}
```

## Linked Data Benefits

So what did all of that get us? The benefits of the Linked Data approach are several.

**Easier data merging and schema migration**. Mashups (combining data from heterogenous sources) become much easier, due to unique IDs and unambiguous properties. The flat graph based structure of most linked data formats (composite structures are expressed via local links, instead of nested documents) also makes merging datasets and schema migration much easier.

Data is **discoverable** (from IDs), **self-describing** and **self-documenting**.

**Reuse and interop**. Unique property names (that are published on the net, well described and schema-specified) encourage interop and reuse, and help cut down on constant wheel reinventing.

**Better searches**. If you embed linked data in your web pages (in JSON-LD format, or embedded in HTML attributes using the RDFa format), Google will actually parse it and use it to enhance search results. See Google's [Introduction to Structured Data ](https://developers.google.com/search/docs/guides/intro-structured-data) for further discussion.

**Rich toolset/ecosystem**. By using an RDF based format, you get a lot of extra tooling and infrastructure for free (in addition to the existing JSON-based toolsets, for example).

## Linked Data Challenges

In going from something like CSV to RDF-based linked data, you've probably picked up on a few implications and challenges to organizing your data in this fashion. Let's go over a few of those.

**URIs**. Giving URIs to things is not always easy.

**Schema discovery**. Discovering, choosing or creating schemas (vocabularies) that fit your use case is sometimes challenging. But at least with linked data, you have the option to browse and study listings/directories of such schemas (unlike with database table schemas, for example). [Schema.org/schemas](https://schema.org/docs/schemas.html) is a good place to start.

**Availability**. Linking to things on the net means you are depending on the uptime of other systems. (See also Leslie Lamport's quote, "A distributed system is one in which the failure of a computer you didn't even know existed can render your own computer unusable".) Fortunately, actually dereferencing the links of properties or IDs is not mission critical, but is more helpful during development and design phases. To put it another way, you can still use `http://www.alice.com/#about` as a good user ID even if Alice's website happens to be down during a given day -- browsing linked data is an additional benefit and possibility, instead of a core operation.
