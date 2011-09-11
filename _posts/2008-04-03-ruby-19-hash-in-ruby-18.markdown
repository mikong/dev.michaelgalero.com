--- 
wordpress_id: 16
layout: post
title: Ruby 1.9 Hash in Ruby 1.8
tags: 
- slug: ruby
  title: Ruby
- slug: point2
  title: point2
- slug: metaprogramming
  title: metaprogramming
categories: 
- slug: open-source
  title: Open Source
- slug: ruby
  title: Ruby
wordpress_url: http://devblog.michaelgalero.com/2008/04/03/ruby-19-hash-in-ruby-18/
---
 I'm learning about Ruby 1.9 features but I realized I won't be able to use it in any of my projects. So I thought of an exercise where I learn Ruby metaprogramming by trying to implement some of Ruby 1.9's new functionality. Aside from learning metaprogramming and Ruby 1.9, I'd also end up with a [library](http://github.com/mikong/point2) that I can use in my Ruby 1.8 projects.But first, let me introduce...### The new Hash
We have an alternative hash syntax in Ruby 1.9:<pre>  # old way that still works in Ruby 1.9  my_hash = { :a =&gt; 'apple', :b =&gt; 'banana' }  # new way  my_hash = { a: 'apple', b: 'banana' }</pre>A nice addition in Ruby 1.9 is that the order in which you added the items to a hash is remembered and will be used when the hash is iterated.There's also a new class method try_convert where if you call<pre>  Hash.try_convert(myobject)</pre>myobject's to_hash method will be called to return a hash. If there's no to_hash, nil will be returned.And then we have these new instance methods (a few were simply borrowed from the Array class): assoc, compare_by_identity, compare_by_identity?, flatten, key and rassoc.### Trying Metaprogramming
Let's first try to implement the try_convert class method. Luckily, [Chris Wanstrath's try() article](http://ozmm.org/posts/try.html) gave us something we could use:<pre>class Object  ##  #   @person ? @person.name :nil  # vs  #   @person.try(:name)  def try(method)    send method if respond_to? method  endend</pre>By building on his code above, we could do this:<pre>class Hash  def self.try_convert(obj)    obj.try(:to_hash)  endend</pre>Looking at that, I'm starting to think Ruby 1.9 should have included the try() method instead of providing try_convert(). But that's beside the point of this exercise.Let's try the simple instance method flatten. The documentation said it converts the hash to an array, then invokes Array#flatten! on the result. So it seems to be simply this:<pre>class Hash  ...  def flatten    to_a.flatten!  endend</pre>But looking closer there's actually a depth parameter (default is 1 - or so it seems but it behaves like -1 in my version of Ruby 1.9) demonstrated in the documentation's example:<pre>h = { feline: [ "felix", "tom"], :equine: "ed" }h.flatten    # =&gt; [:feline, ["felix", "tom"], :equine, "ed"]h.flatten(1) # =&gt; [:feline, ["felix", "tom"], :equine, "ed"]h.flatten(2) # =&gt; [:feline, "felix", "tom", :equine, "ed"]</pre>It turns out that the flatten and flatten! methods in Ruby 1.9 Array has also changed with a new level parameter. The default value of -1 makes it behave like the original (i.e. it recursively flattens the array). A level value of 0 performs no flattening and a level greater than zero flattens only to that depth (like the depth parameter in the Hash#flatten example above).The flatten method we implemented above works fine. If you want the depth parameter, I've added it by first redefining the flatten method in Array, and then just calling that from the flatten method in Hash. You could head over to my [GitHub repository](http://github.com/mikong/point2) to see the code. The simple project also implements some Ruby 1.9 Time class methods (sunday?, monday?, etc) using the method_missing trick. And I'll continue to play around with Ruby 1.9 and metaprogramming so you could expect more Ruby 1.9 features in Ruby 1.8. The idea is not to port Ruby 1.9 to Ruby 1.8 (that would be crazy!), but to try stuff so some weird things may also crop up.
