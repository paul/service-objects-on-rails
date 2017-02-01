---
title: Service Objects on Rails
---

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

[decorator-object]: /decorators.html
[presenter-object]: /presenters.html
[form-object]:      /form-objects.html
[command-object]:   /command-objects.html
[conductor-object]: /conductors.html
[query-object]:     /queries.html
[performer-object]: /performers.html
[injector-object]:  /injectors.html
[mold]:             /mold.html



