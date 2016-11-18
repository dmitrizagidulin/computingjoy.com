> Every program attempts to expand until it can read mail. Those programs which cannot so expand are replaced by ones which can.

> [Zawinski's Law of Software Envelopment](https://en.wikipedia.org/wiki/Jamie_Zawinski#Zawinski.27s_law_of_software_envelopment)

I wonder if there's a similar law having to do with APIs, or backup/export functionality, or open standards. If there is not, there *should* be. Something like "Law of Software Superiority".

### Rules of Software Superiority

It comes down to this. If you have a choice of which software (or web app or service) to use:

* Choose the one with an API over the one that doesn't
* If choosing between two APIs, pick one that uses standard protocols and data formats, or is an open standard itself
* Choose one that has an Export/backup functionality
* Choose one that stores its data in a standard (spec'd out or well understood) format
* Choose one that is open-source over one that isn't. Hell, go wild, and choose *free software* (in the Stallman sense) over merely open source (and of course over proprietary)
* That said, if the choice is between free software that uses an obscure/undocumented data format (that is, it's not clear how you'd get your data *out*) and proprietary software that has a clear backup/export strategy to an understandable format... *choose the proprietary one*. The safety and longevity of your data is of paramount importance.

These preferences (libre > open-source > proprietary, api > backup/export > neither, open data format > proprietary/undocumented format) are not about idealism (unless that's your thing, in which case, by all means). They are about *reducing risk*. Chances are incredibly high that:

* If you're using an online service or a commercial app, the company will go out of business, or even more likely, will get acquired or merge with another (which will not care about product continuity).
* If the company doesn't change hands, the project (or app or service) will get discontinued, or pivot to a direction you don't like.
* Your account can be locked out or revoked at any time, due to over-zealous content filtering, accounting errors, etc.
* Even for open-source projects, the project leadership may abandon it and move on, or again, take it in a direction that makes it unusable to you.

This is not meant to discourage you, or sound overly pessimistic. This is just a simple reality of living with software. My point is that if you consistently choose software and services that make it possible to get your data *out*, you can be at peace with all these eventualities.

Ok, so, the API thing... The important thing here is to be able to get the data out of the software. (And, only secondarily useful for actually scripting / extending it / mashups.) If you can get to the data (because it lives on your computer's hard drive) but can't use it (it's in an undocumented/proprietary format), it's no good. Similarly, if it's in a simple or well-understood format (say, blog posts, or pictures or whatever) but you can't get to it (ahem, I'm looking at you, various social media sites), again, useless. At best, you will have to resort to the nightmare of screen-scraping.

### Data-Centric Software

There is a type of software that, by its very nature, lends itself well to preserving the longevity of your data, and compares favorably in terms of the above rules. And that is *data-centric* software, where the user has control over data in a well-understood and documented format, and thus has a choice of which apps to use to work with it. Chances are you're very familiar with this sort of model, just from using popular desktop applications. Examples include text editing and word processing, spreadsheets, presentations, image editing. Personal accounting software (Quickbooks and the like) is actually almost there, although there are occasional subtle incompatibilities with formats.

The situation gets a lot more difficult (in terms of users being able to control their data, and be able to have a choice of interchangeable/competing applications) once you get into online services and applications, and mobile apps. In the world of traditional desktop software, data lives on generic storage (hard drives, etc), and most applications have equal access to it. In the mobile app world, aside from a small handful of standardized shared resources (basically, your camera roll), storage mechanisms are a lot more opaque, and each app is encouraged to use its own individual slices of storage.

And with online services and social media apps, the situation is even worse. Unless a service is progressive enough to expose a comprehensive API, your data is pretty much locked in there, and good luck using third-party services with it.

It doesn't have to be this way, though. There is nothing about web apps or mobile apps that inherently forbids them from being data-centric, or makes interoperability impossible. These days, browser applications can actually have access to your hard drive (if you let them), and cheap cloud service providers can serve the role of generic data stores (just like your computer's hard drive). Your data (whether it's documents or images or even social media type things like blog posts and status updates) *could* be under your control, and you could have the choice of interoperable competing software with which to edit it.

This goal of enabling interoperable data-centric web applications that use generic storage (local or cloud-based) is one of our main motivations at the [Solid project](https://solid.mit.edu/) ([project repo](https://github.com/solid/solid) | [specs](https://github.com/solid/solid-spec)). (There are other goals, like making decentralized app development easier, encouraging the use of [linked data](http://computingjoy.com/blog/2016/09/26/understanding-linked-data/), breaking the deadlock of various social media monopolies, and so on.) It's an ambitious project, and involves a lot of interesting engineering and research challenges (as does all large-scale decentralized software). I'll get into some of the technical challenges (and our solutions) in subsequent posts.
