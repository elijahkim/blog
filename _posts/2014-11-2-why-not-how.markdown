---
layout: post
title: "Learn Why And How"
subtitle: "How to learn to code"
date: 2014-11-02 18:02
categories: how-to
---

As a Mathematics major now studying code, I've learned that there are efficient
ways to learn and the less efficient ways. The difference is
[relational understanding vs instrumental understanding](http://www.grahamtall.co.uk/skemp/pdfs/instrumental-relational.pdf). 

In essence, relational understanding is "knowing how and why" as opposed to
instrumental understanding which would be described as "rules without reason".
To understand the differences, think way back to the elementary school days when
you had to learn the multiplication table. You might have memorized everything
from 1x1 to 12x12 and were able to recite any multiple in between. However, to
know why 3 times 4 was 12 you had to know that it meant to add 3 to itself 4 times. Knowing
the latter provided you with much more powerful tools than just knowing the
multiplication tables. By knowing what multiplication actually was, you were
able to do far more than just 12x12 but 13x13 or 42x3. Just finding those
answers was a tedious task.

To really know how to code, you must have a relational understanding of what
code actually is. Don't worry so much about syntax. Syntax is always documented.
And as long as you know what you need, you can find the syntax in the docs.
If you're still having trouble after reading the docs, you can go to other resources like [Stack
Overflow](http://stackoverflow.com/). However, if you don't even know what you're searching for, you're
gonna have a bad time.

![gonna have a bad time.jpg](http://www.memecreator.org/static/images/templates/14603.jpg)

Instead, when reading code, learning code, or writing code, think about how the
code works.

As an example, let's look at something that every rails programmer has used. The
`has_many` relationship. It's a necessity in back end development, but not
everyone knows how it works. We all know that in order to have one model relate
to many objects in another model, we need a has many relationship, such as a
team that has many players. This is pretty much known fact, and common
knowledge. What more do we have to do but put one line of code in the Team
Model?

{% highlight ruby %}
has many :players
{% endhighlight %}

But what if a player can be on many teams? Or, let's get a little
crazy, a player has many teammates? Holy cow. Hold your horses. Things just got
super complicated. A simple has_many relationship won't work here. We're going
to need a many to many through for the player to also have many teams and then a
many to many self for a player to have many teammates. Where I see many coders
having trouble is right here. They always try to memorize the syntax instead of
relying on logic. Even
worse, some might not even know what sort of relationships are needed because
they were too worried about memorizing syntax that they forgot to learn about
the overarching concepts!

What I'm saying here is to just know what you need at any given point of your program.
You can always, ALWAYS, find syntax documented everywhere online. But let's take
this analogy one step farther. Learn the mechanics of a simple has many
relationship
and you'll pretty much understand how every has many relationship
works. Better yet, you'll be able to write out each relationship as well!

So what's the difference between relational understanding and instrumental
understanding? In my true and honest opinion, instrumental understanding isn't
actually understanding at all. Instrumental understanding relies heavily on
memorization and when things come down to the nitty-gritty, pure memorization
will fall apart. Instead, strive for relational understanding. Know the inner
workings of whatever you're learning, whether it is something simple such as
multiplication or something that we as a human race might never understand like
quantum physics. Saying "I memorized the formula" is never enough.

So what if I can't achieve relational understanding?

I had this problem so many
times in my analysis courses that I was worried I would fail. And at these
times, I at least tried to get the instrumental understanding down in hopes that
later I will actually realize what is going on. Eventually, I had some "Ohhh"
moments and certain things started to make sense. In the same way, if things
seem impossible to understand, don't surrender. Know that later on as you grow
as a programmer, you might have to connect some dots in order to really 
understand what is going on. But in the end, you'll come out as a much stronger
coder.

