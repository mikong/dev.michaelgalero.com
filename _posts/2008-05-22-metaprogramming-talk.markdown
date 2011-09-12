--- 
wordpress_id: 17
layout: post
title: Metaprogramming Talk
tags: 
- slug: ruby
  title: Ruby
- slug: metaprogramming
  title: metaprogramming
- slug: ruby2ruby
  title: ruby2ruby
categories: 
- slug: ruby
  title: Ruby
wordpress_url: http://devblog.michaelgalero.com/2008/05/22/metaprogramming-talk/
---

I'd like to thank everyone who attended the Philippine Ruby Users Group (PHRUG) May '08 Meetup and listened to my over-2-hour talk on Metaprogramming. Thanks for the patience, for not sleeping (or not making it obvious), and for those wonderful questions. It's tough discussing the object model of Ruby when terms are repeated like 'the superclass of the metaclass of the Ninja class is the metaclass of the Object class'. I literally had a headache after the talk.

### Talks

As I mentioned, my talk was largely based off of [Dave Thomas' "Metaprogramming" talk](http://www.infoq.com/presentations/metaprogramming-ruby) in QCon London '06 and [Patrick Farley's "Ruby Internals" talk](http://mwrc2008.confreaks.com/11farley.html) in the MountainWest Ruby Conference (MWRubyConf) '08. Click on the links to see/download their presentations.

There are [other talks related to metaprogramming in MWRubyConf '08](http://mwrc2008.confreaks.com/) like those by Giles Bowkett, Jeremy McAnally, and Joe O'Brien. Or just go to the [Confreaks site](http://www.confreaks.com/) to check other cool talks on Ruby.

### Coding Session

I had a problem with Ruby2Ruby.translate at one point in the coding session. I've just confirmed what was pointed out to me. Indeed, Ruby2Ruby couldn't translate a class without a method definition. What you all wanted to see was what would this:

<pre>
  module Taijutsu
    def punch
      puts 'punch'
    end
  end

  class Ninja
    include Taijutsu
  end
</pre>

look like when Ninja is translated using Ruby2Ruby. Here it is:

<pre>
  class Ninja &lt; Object
    def punch
      puts 'punch'
    end
  end
</pre>

Unfortunately, Ruby2Ruby won't show an include call, as a lot of you had hoped.

### Slides

I've uploaded the pdf of my slides at the [Files section of our PHRUG Google Group](http://groups.google.com/group/ruby-phil/files). But you might be better off watching the talks I mentioned above.

Minor note: I used back quotes in my slides to refer to singleton classes and metaclasses. I thought this was the standard but looks like it's not. Patrick Farley used the normal single quotes before the names in his slides. The pickaxe book used single quotes after the class name (for metaclass) and 'anon' which means anonymous class (to refer to the singleton classes and include classes of modules).

### Thanks

Thanks again! If you enjoyed my talk, you might want to [recommend me at Working with Rails](http://workingwithrails.com/recommendation/new/person/8924-michael-galero). And you might want to check out this article by [Ola Bini on Dynamically created methods in Ruby](http://ola-bini.blogspot.com/2008/05/dynamically-created-methods-in-ruby.html).
