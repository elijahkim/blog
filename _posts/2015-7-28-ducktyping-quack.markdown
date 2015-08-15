---
layout: post
title: "Ducktyping"
subtitle: "Quack"
date: 2015-7-27 17:57
categories: how-to
---

I was working on one of our [projects](https://www.bakecycle.com) when I
happened to run across some code that set off my rubber ducky detector. It's a
page that displays the days production quantities or displays an estimate if
it's too early in the day to tell. Both cases have very similar views and they
share a common interface. Our controllers were too fat and our views knew too
much.

Here's the culprit.

{% highlight ruby %}
class ProductionRunsController < ApplicationController
  def print_recipes
    @date = date_query
    @production_run = production_run_for_date(@date)
    @projection_run = ProductionRunProjection.new(@date) unless @production_run
  end
end
{% endhighlight %}

{% highlight erb %}
# view
<h1>Print Recipes <%= @date.strftime("%A %b. %e, %Y") %></h1>
<% if @production_run %>
  <%= render 'production_run', object: @production_run %>
<% else %>
  <%= render 'projection_run', object: @projection_run %>
<% end %>
{% endhighlight %}

We have this one view that renders either a `projection_run` or a
`production_run` depending on if a user has a production run on that date or
not. The code works but I have a few problems with it.

- We have 3 instance variables in 1 controller action. One of which will always
  be nil! We literally have a variable holding nil. This totally breaks Sandi
Metz rule of having 1 instance variable per action. Hard

- More importantly, what happens later on down the road if the client we're
  building this for wants `past_runs` to be formatted differently? Continuing
this pattern, it would probably look something like this.

{% highlight ruby %}
class ProductionRunsController < ApplicationController
  def print_recipes
    @date = date_query
    @past_run = past_run_for_date(@date)
    @production_run = production_run_for_date(@date)
    @projection = ProductionRunProjection.new(@date) unless @production_run
  end
end
{% endhighlight %}

{% highlight erb %}
# view
<h1>Print Recipes <%= @date.strftime("%A %b. %e, %Y") %></h1>
<%= render 'past_run', object: @past_run if @past_run %>
<%= render 'production_run', object: @production_run if @production_run %>
<%= render 'projection_run', object: @projection_run if @projection %>
{% endhighlight %}

As the conditions that the client gives get greater and greater, this view will
get more `if`s and more variables in the action. As a best practice, it is good
to have the least amount of logic in the views as possible and this has a lot.

A more elegant solution involves two patterns: the **facade pattern** and **duck
typing**. 

##### The facade pattern
</br>
The facade pattern is pretty much what it sounds like. We're hiding logic in
some sort of object and asking that object to do work for us rather than the
controller. Doing this allows us to stick to one variable and please the ever
lovely Sandi Metz. A quick refactoring might looking something like this:

{% highlight ruby %}
class ProductionRunsController < ApplicationController
  def print_recipes
    @facade = ProductionRunFacade.new(@date)
  end
end

class ProductionRunFacade
  attr_reader :date

  def initialize(date)
    @date = date
  end

  def production_run
    @_production_run ||= production_run_for_date(date)
  end

  def projection
    @_projection_run ||= projection_run_for_date(date)
  end
  
  private

  def production_run_for_date
    #some logic
  end

  def projection_run_for_date
    #some logic
  end
end
{% endhighlight %}

{% highlight erb %}
# view
<h1>Print Recipes <%= @facade.date.strftime("%A %b. %e, %Y") %></h1>
#stuff omitted
<% if @facade.production_run %>
  <%= render @facade.production_run %>
<% else %>
  <%= render @facade.projection %>
<% end %>
{% endhighlight %}

It's getting there but it could use a bit more love. Let's move the date logic
from the view to the facade.

{% highlight ruby %}
class ProductionRunFacade
  attr_reader :date

  #code omitted

  def formatted_time
    date.strftime("%A %b. %e, %Y")
  end  
end
{% endhighlight %}

{% highlight erb %}
# view
<h1>Print Recipes <%= @date.formatted_time %></h1>
{% endhighlight %}

Here's a small win! and it's all about those small wins. We can take this a step
farther but first let's talk about Duck Typing.

#####Duck Typing#####
</br>

Sandi can explain duck typing better than I can. In her words,

> Duck types are public interfaces that are not tied to any specific class.
> These across-class interfaces add enormous flexibility to your application by
> replacing costly dependencies on class with more forgiving dependencies on
> messages.

> Duck types objects are chameleons that are defined more by their behavior than
> by their class. This is how the technique get its name; if an object quacks
> like a duck and walks like a duck, then its class is immaterial, it's a duck.

An example that you may all be familiar with is the `[]` operator. 

{% highlight ruby %}
array = [0,1,2,3,4]
string = "hello world"
array[0] #returns 0
string[0] #returns h
array[1..-1] #returns [1,2,3,4]
string[1..-1] #returns "ello world"
{% endhighlight %}

We can call the `[]` operator an both an array and a string, `[]` is the quack
and both objects are ducks.

Now let's apply this to our code.


{% highlight ruby %}
class ProductionRunFacade
  attr_reader :date

  def initialize(date)
    @date = date
  end

  def run
    @_run ||= production_run || projection_run
  end

  def formatted_time
    date.strftime("%A %b. %e, %Y")
  end  

  private

  def production_run
    #some logic
  end

  def projection_run
    #some logic
  end
end
{% endhighlight %}

{% highlight erb %}
# view
<h1>Print Recipes <%= @facade.formatted_time %></h1>
<%= render @facade.run %>
<% end %>
{% endhighlight %}

There are a couple things that happened here. First, `@production_run` and
`@projection_run` became one variable called `@run`. Secondly, we no longer have
any measly `if`s in our view anymore, we only call  `render` on `@facade.run`.
What is this magic? Here we are using the power of duck typing and trusting that
`ProductionRun` and `ProjectionRun` have implemented `to_partial_path` which
`render` calls. Because of this trust we no longer care what the object is but
care about the messages it sends. 

With this new architecture, adding new potential runs is made extremely easy.
Actually, let's implement past_runs as well! It's only a few additional lines of
code.

{% highlight ruby %}
class ProductionRunFacade
  attr_reader :date

  def initialize(date)
    @date = date
  end

  def run
    @_run ||= production_run || projection_run || past_run
  end

  def formatted_time
    date.strftime("%A %b. %e, %Y")
  end  
  
  private

  def past_run
    #some logic
  end

  def production_run
    #some logic
  end

  def projection_run
    #some logic
  end
end
{% endhighlight %}

Done. We don't have to add any more `if`s in our view and we don't have to add
any additional instance variables in our controller. Easy as quack.
