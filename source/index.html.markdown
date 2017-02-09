---
title: Service Objects on Rails
---

# Service Objects on Rails

There have been much digital ink spilled about service objects in Ruby on 
Rails; what they are, how to use them, and which gems are the most popular. In
this series, I hope to explain which ones I've found the most useful, along with
concrete examples of refactorings, starting with issues often found in "vanilla"
Rails applications, and how the proper application of a particular service
object in that situation can resolve those issues.

Much of this is inspired by [Patterns of Enterprise Application Architecture (PoEAA)][poeaa], 
and similar to that text's premise, these Service Objects are extracted from
patterns that I've personally found useful in writing medium-sized Rails apps. 
Who am I to write about this? I've been writing Rails applications professionally for 12 years, starting with 0.11 in 2005. I remember they heady days of "Skinny Controller, Fat Models". I was very involved in experimenting with alternative frameworks, and a core contributor to the Merb and DataMapper projects. I've worked on some of the largest Rails monoliths in existence, and been involved in cleanups, refactorings and extracting microservices. I don't claim to know everything there is to know about Rails, but I've certainly seen a lot, and my goal with these pages is to share this experience and I hope you find it useful. I'm always on the lookout for new techniques and patterns to write clean and pragmatic Rails applications, so I do hope you'll share some back with me.

Each section in this series will follow a similar pattern: First, I'll introduce the particular service object, and when it is applicable. Then, I'll have some example code written in the "vanilla" Rails-style that could be improved by the service object, and through a series of refactorings, arrive at the final implementation. Since one of the primary advantages of Service Objects is to make complicated logic more testable, I'll also write some simple test examples. 

Since my intention is to demonstrate the application of the Service Objects, I will strive to write them as PORO (plain ol' Ruby objects) at first, and only introduce other libraries if needed. However, if there is a particular gem that I recommend which provides a nice implementation of the pattern, I'll be sure to include links.

If you spot any issues, please feel free to hit me up on [email][email] or [twitter][twitter], or even better [open an issue][issues] in [this repo on Github][github]. Ok, enough rambling, on to the Service Objects! (hold your applause, please)

## Service Objects

* [Decorator][decorator-object] Wraps one or more models with some basic
  presentation logic.
* [Presenter][presenter-object] Handles a single aspect of presentation logic,
  usually without wrapping the model.
* [Form Object][form-object] Useful when a form involved several model objects,
  particularly when nested. Also useful when the form validations aren't 1:1
  with the database fields, like `password_confirmation`.
* [Command Object][command-object] When submitting a form involves several
  model objects, or has several side effects like sending emails or enqueuing
  background jobs.
* [Conductor][conductor-object] If you need both a [Form Object][form-object]
  and [Command Object][command-object], but neither are very complex, then you
  can roll them together into a since [Conductor][conductor-object]
* [Query][query-object] Used when loading several models through associations,
  they're useful to avoid accidental N+1 queries. Also helpful for complex
  queries that can them be chained with other query objects or simple
  ActiveRecord scopes.
* [Performer][performer-object] Use when there is a complicated series of steps
  or business logic that needs to be performed. Often these encompass other
  service objects, and allow useful hooks for testing.
* [Injector][injector-object] A handy bucket of named objects, useful for
  dependency injection, and substituting mock objects into the various service
  objects.

## Other useful utilities that aren't strictly Service Objects

* [Mold][mold] A very simple form builder that concerns itself only HTML
  attributes and setting the value property. This exists because I found
  `simple_form` and the like to be too complicated and opinionated. Having
  everything in a global config file is too inflexible.

[poeaa]: https://www.martinfowler.com/books/eaa.html

[email]: mailto:paul@sadauskas.com
[twitter]: https://twitter.com/TheAmazingRando
[issues]: https://github.com/paul/service-objects-on-rails/issues
[github]: https://github.com/paul/service-objects-on-rails

[decorator-object]: /decorators.html
[presenter-object]: /presenters.html
[form-object]:      /form-objects.html
[command-object]:   /command-objects.html
[conductor-object]: /conductors.html
[query-object]:     /queries.html
[performer-object]: /performers.html
[injector-object]:  /injectors.html
[mold]:             /mold.html



