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

The easiest way to do this is making a ridiculous number of if statements.

{% highlight ruby %}
###
match_history view
most of the view omitted
###
<% @matches.each do |match| %>
  <div class=<% if match.winner == current_user ? "winning_class" : "losing_class">
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

I'm sure you can all find a myriad of problems with this code. It is completely
functional, but honestly, who wants to see that?
