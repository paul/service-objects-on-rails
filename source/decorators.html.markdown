---
title: "Service Objects on Rails: Decorators"
---

# Decorators

A decorator is useful when we need to wrap a business object, most likely a
model, for use in a view. A common example is the display of a user's name in a
header as a greeting, when there are many possibilities on how to display it.

> Good Morning, Paul

Obviously, we'll need a way to know the current time, and there might be several edge-cases involved:

 * What if the user model has an optional `first_name` field? We should fall
   back to `email`, but then the greeting makes no sense, so we should leave it
   out.

## The Naïve Approach

Let's start out with a naive approach, and put the greeting in the model.

```erb
<% if user.first_name.present? %>
  <%= user.salutation %>, <%= link_to user.first_name, user_path(user) %>
<% else %>
  <%= link_to user.email, user_path(user) %>
<% end %>
```

```ruby
# app/models/user.rb
class User
  # ...

  def salutation
    case Time.now.hour
    when 6..12  then "Good Morning"
    when 12..17 then "Good Afternoon"
    when 17..20 then "Good Evening"
    else             "Hello"
    end
  end

  # ...
end
```

It's not terrible, and perhaps if this is a simple app with only a handful of
views and models, this approach would be fine. However, in a more complicated
app, this approach has several shortcomings:

1. The view has a lot of mental overhead. In this isolated example, this isn't
   too bad, but if this `if` is nested inside several other tags and `if`s,
   then keeping track of it all can get complicated.
1. Additionally, the only way to test the "display first name, otherwise
   display email" behavior is to write a test for the entire controller action
   with a lot of extra setup, or an expensive and slow Cucumber/Capybara test.
   **Logic in views/templates is hard to test.**
1. The model gets an extra method that has nothing to do with the database,
   its presentation logic that isn't dependent on the state of the `user`
   object at all.
1. This is also hard to test, because the output of this function depends upon
   the time of day that it is run. We have to rely on hacks like `#travel_to`
   or Timecop.


## Refactor 1

Let's start by getting this logic out of the view. The standard way recommended
by the Rails Guides is to use a helper, so we'll start there.

```erb
<%= greeting(user) %>
```

```ruby
# app/helpers/user_helper.rb
module UserHelper
  def greeting(user, time = Time.now)
    if user.first_name.present?
      salutation(time) + ", " +
        link_to(user.first_name, user_path(user)
    else
      link_to(user.email, user_path(user)
    end
  end

  def salutation(time = Time.now)
    case Time.now.hour
    when 6..12  then "Good Morning"
    when 12..17 then "Good Afternoon"
    when 17..20 then "Good Evening"
    else             "Hello"
    end
  end
end
```

We've just simply pulled the `if...else` out of our template and copied it into
a helper. This is a definite improvement, take a look at how it resolves the
concerns from before:

1. The view is trivial, just one line of code. Doesn't get any simpler than
   that.
1. Testing it is easier, we can do it in a regular TestUnit or RSpec test. It
   is a little cumbersome that we need to create an empty class and include the
   module. (This smell is a big indicator of the next step!)
1. We've moved the `#salutation` method out of the model, and into the helper
   which is a much better place for this presentation logic.
1. We could have done this step in the model, but we modified the `#salutation`
   method to take the current time as an argument. This will allow us to
   explicitly set it in a test, and test the logic against a known time.


Let's make a `Decorator` to handle this. It can be just a PORO (Plain Ol' Ruby
Object) for now, but there are helper libraries with addition features, like
[Draper][draper].

```ruby
class UserGreeting
  attr_reader :user

  def initialize(user)
    @user = user
  end

  def greeting(time = Time.now)
    "".tap do |text|
      text << salutation(time) << ", " if include_salutation
      text << display_name
    end
  end

  private

  def include_salutation?
    user.first_name.present?
  end

  def salutation(time)
    case time.hour
    when 6..12  then "Good Morning"
    when 12..17 then "Good Afternoon"
    when 17..20 then "Good Evening"
    else             "Hello"
    end
  end

  def display_name
    user.first_name.presence || user.email.presence
  end
end
```

This is not much code and it is not particularly complicated. However it would be out-of-place to live in the User model, because it is presentational logic, and has nothing to do with the database layer. It would also be complicated to put in a stateless helper method because the `user` would need to be passed around into several places.




[draper]: https://github.com/drapergem/draper



