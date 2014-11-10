---
layout: post
title: "R is for Refactor"
subtitle: "Make Ruby Beautiful"
date: 2014-11-08 06:42
categories: how-to
---

One of the key characteristics of Ruby is it's amazing readability. However, not
everyone that uses Ruby is able to utilize it to it's full readable potential.

Luckily, week 7 of our boot camp was solely dedicated to refactoring. Even more
lucky, we were graced with an amazing speaker and programmer,
[Tute](https://twitter.com/tutec) who taught us all about refactoring.

###Abstract out logic###

Just to start, how about an example of bad code verses good code. 
Let's write some simple code to check an array of number is divisible by 3.

{% highlight ruby %}
myarry.each do |number|
  if number % 3 == 0 
    puts number
  end
end
{% endhighlight %}

This is somewhat understandable. But what if we did this instead?

{% highlight ruby %}
myarry.each do |number|
  if divisible_by_3? number
    puts number
  end
end

private

def divisible_by_3?(number)
  number % 3 == 0
end
{% endhighlight %}

By extracting logic out of loop, we are able to clearly understand what we are
checking for. Furthermore, because it is so explicit what the code is doing,
we have no need for comments. And although it may seem like a very simple thing,
never underestimate the importance of naming. 

###The NullObject Pattern###

The nil object is everywhere and everywhere, it leaves chaos. How many time's
have you received this error?

{% highlight ruby %}
##undefined method for nil:NilClass
{% endhighlight %}

God, That is always frustrating. And usually, this error doesn't help too much.
Instead, let me introduce the `NullObject`. This baby is sweet. It's awesome.
Let me show you through this example.

{% highlight ruby %}
selected_hotel = Hotel.find{ |h| h.name == query }
puts selected_hotel.show
{% endhighlight %}

Hopefully, I've refactored this enough so that you know what is going on.
The code works beautifully, at least until the query doesn't fail. When it does,
we're going to get that dreaded undefended method for NilClass garbage again.
Sure we could add a if statement but who wants to see that? It's so dirty and
clunky. 

{% highlight ruby %}
 hotels.each do |hotel|
   if hotel.name == query
     selected_hotel = hotel
   else
     puts "hotel does not exist"
   end
 end

puts selected_hotel.show
{% endhighlight %}  
  
No one wants that, instead, let's do something like this.

{% highlight ruby %}
selected_hotel = Hotel.find{ |h| h.name == query } || NullHotel.new
puts selected_hotel.show

class NullHotel
  def show
    "No Hotel Found"
  end
end
{% endhighlight %}

HOW AWESOME IS THAT! This is called the NullObject Pattern.
The NullObject Pattern relies heavily on the polymorphic nature of Ruby and is
called duck typing. In essence, if it looks like a duck, and it quacks like a
duck, it must be a duck. So although the `NullHotel` is not an actual hotel, it
has methods that make it seem like a hotel. At least it has a show method like a
hotel does so we're able to assume that it is a hotel and call show on it. It's
Great! So learn it, use it, and make your code beautiful. 

###Abstract Out your Classes###

A class should take be short, sweet, and have a single responsibility. A way to
make this happen is to abstract out your classes. Let's take for example, a deck
of cards.

{% highlight ruby %}
class Deck
  ##most of the class omitted

  def create_deck(values, faces)
    values.each do |value|
      faces.each do |faces|
        create_card(face, value)
      end
    end
  end

  def create_card(face, value) 
    deck << {face: :face, value: :value}
  end
{% endhighlight %}

You wouldn't want to have a class Deck that is responsible for being a deck,
creating cards, or even knowing what a card is! A deck should be a simple thing,
a collection of cards. So instead, a Deck class might look something like this.

{% highlight ruby %}
class Deck
  ##most of the class omitted
  def create_deck(values, faces)
    values.each do |value|
      faces.each do |face|
        deck << Card.new(value, face)
      end
    end
  end
end

class Card
  attr_accessor :value, :face

  def initialize(value, face)
    @value
    @face
  end
end  
{% endhighlight %}

By splitting up the Card and the Deck class, things are so much easier to
understand. We also have short and concise classes instead of a huge monolithic
wall of text.

The advantages of brief classes far outnumber the pain of refactoring. First and
foremost, it makes your code much more readable. It's welcoming and beautiful.
You want to understand what is going on as opposed to the huge class that we had
before. Also, we can easily change or add functionality to classes that are
nicely refactored because we know that each class has a unique job. Lastly, we
with shorter and single responsibility classes, testing is a breeze! If you find
that testing is hard to do, your classes are probably too big.

###Sandi Metz Rules:###

Before I end this post, I will leave you with 4 rules that you should pretty
much take as the commandments. Follow these rules and you will be well on your
way to refactoring nirvana. Although there will be times when you need to break
these rules, these are some great rules to live by.

1.  Classes can be no longer than one hundred lines of code.
2.  Methods can be no longer than five lines of code.
3.  Pass no more than four parameters into a method. Hash options are parameters.
4.  Controllers can instantiate only one object. Therefore, views can only know
about one instance variable and views should only send messages to that object
(@object.collaborator.value is not allowed).




