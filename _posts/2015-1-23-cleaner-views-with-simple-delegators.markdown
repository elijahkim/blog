---
layout: post
title: "Simplify your views with simple delegators"
subtitle: "Get rid of those pesky if statements"
date: 2015-1-23 16:12
categories: how-to
---

While working on my app, Tennis Match, I decided to make a match history page.
It would display your recent matches and some information pertaining to them
such as if you won or lost, how many elo points you gained or lost, who you
played, and when. I also wanted each match history log to be seperated by boxes
and be green if you won and red if you lost.

This was the end result:
![alt-text](https://cloud.githubusercontent.com/assets/8673900/5492223/87060d8c-86ad-11e4-9c64-941f86a40754.png)

The easiest solution is making a ridiculous number of if statements.

{% highlight %}
#match_history view
#most of the view omitted

<% @matches.each do |match| %>
  <div class=<% if match.winner == current_user ? "winning_class" : "losing_class" %>>
    <div><%= match.challenger %> vs <%= match.defender %></div>
    <div><%= match.match_at %></div>
    <% if match.winner == current_user %>
      <div>+ <%= match.elo_difference></div>
    <% else %>
      <div>- <%= match.elo_difference></div>
    <% end %>
    <%= link_to "Match Link", match %>
  </div>
<% end %>
{% endhighlight %}

But we're doing Ruby! This is some ugly ruby. There's a crap ton of logic in the
views and... a ternary? are you insane?

Intro, (Simple
Delegators)[http://ruby-doc.org/stdlib-2.1.0/libdoc/delegate/rdoc/SimpleDelegator.html].
 "A concrete implementation of Delegator, this class provides the means to delegate all supported method
calls to the object passed into the constructor and even to change the object
being delegated to at a later time with "

Using this, we can make decorators to clean up our views.

Let's start with the controller

{% highlight ruby %}
#match_history_controller
def index
  @matches = []
  Match.each do |match|
    if match.winner == current_user
      @matches << WinningMatch.new(match)
    else
      @matches << LosingMatch.new(match)
  end
end
{% endhighlight %}

Then we can make these decorated matches

{% highlight ruby %}
class WinningMatch < SimpleDelegator
  attr_reader :match

  def initialize(match)
    @match = match
    super(@match)
  end

  def match_outcome_class
    "match-stats-winner"
  end

  def elo_delta
    "+ #{match.elo_delta}"
  end

  def win_or_lose
    "won"
  end
end
{% endhighlight %}

We would do exactly the same for `LosingMatch`.

We would end up with an array of matches in our `#index` action
that would be either a `LosingMatch` and `WinningMatch`s. The awesome conclusion
to this is that we can call the same method to either `WinningMatch` or
`LosingMatch` and they would know exactly what to return. This is called duck
typing.

So in the end, we can create a partial like this

{% highlight %}
<li class="<%= match.match_outcome_class %>">
<div class="match-stats">
  <p class="username"><%= match.challenger_username %> vs. <%= match.defender_username %></p>
  <p><%= match.match_at.strftime("%F") %></p>
  <p><%= match.win_or_lose %></p>
  <p><%= match.elo_delta %> </p>
  <p class="match-link"><%= link_to "Match Link", match_path(match) %></p>
</div>
</li>
{% endhighlight %}

In conclusion, we were able to take out the various if statements and employ simple
delegators and duck typing to create a clean partial with absolutely no
branching. I'd say that is an enormous win.
