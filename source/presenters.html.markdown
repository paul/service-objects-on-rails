---
title: Presenters
---

A Presenter is similar to a [Decorator][] in that handles some aspect of an
objects display, however it does not transparently wrap and pass through the
methods of the underlying object. Additionally, it is capable of handling
several related objects, where a decorator usually handles only one.

I like using presenters whenever I have a model that can have several "types",
and each type has a different way to be displayed. For example, in a CRM, there
is often a model representing an "Asset", which may be an image, or a movie, or
event just a block of text.

The naïve approach in Rails would be to use a chained `elsif` statement in the
view, or maybe having a different partial for each type:

```erb
<%= render partial: "asset/#{@asset.type}" %>
```





