--- 
wordpress_id: 4
layout: post
title: An RSpec example for code you should not write
tags: 
- slug: rspec
  title: rspec
- slug: bdd
  title: bdd
categories: 
- slug: rails
  title: Rails
wordpress_url: http://devblog.michaelgalero.com/2007/11/16/an-rspec-example-for-code-you-should-not-write/
---
I have very recently started practicing Behaviour-Driven Development (BDD) using RSpec on Rails. RSpec is a framework "to describe the behaviour of Ruby code with readable, executable examples that guide you in the design process and serve well as both documentation and tests" (definition taken from the [RSpec project home page](http://rspec.rubyforge.org/index.html)). A programmer practicing BDD goes through cycles of describing a behaviour, writing the test for it, before finally writing the code to implement it. If you need more introduction to BDD and using RSpec, head over to [Rails Envy for a good presentation on this](http://www.railsenvy.com/2007/10/4/how-i-learned-to-love-testing-presentation).In that presentation, Gregg Pollack mentioned that it's good to have tests for every line of code. I've been thinking about that and while writing a particular RSpec example, I realized I just wrote something that tests... well... a code I should not write. Please consider this case I'll describe.Let's say there's a time-box called Foobar where a team needs to work on a list of tasks. Since it's a definite period of time, the team has a limited number of manhours. The tasks defined in the list have estimated hours needed to finish it. That means tasks can only fit in the Foobar if their total estimated hours are within the limited number of manhours, right? Initially, this was thought to be the case. But it was later specified that the tasks' estimated hours are only estimates, and so a requirement was added that the Foobar should be allowed to have a list of tasks where the total estimated hours exceed the total manhours available.The RSpec example code could look something like this:<pre>describe Foobar do  fixtures :foobars  ...  # Testing for code you should not write  it "should be allowed to have tasks where the total estimated hours exceed the Foobar period's total manhours" do    foobars(:thirty_day_foobar).tasks&lt;&lt;(Task.new(:desc =&gt; "Task 1", :estimated_hours =&gt; 10))    # This 2nd task has an estimate of 1 million hours.    # You'd need about 1400 people working 24 hours-a-day for 30 days to do this.    foobars(:thirty_day_foobar).tasks&lt;&lt;(Task.new(:desc =&gt; "Task 2", :estimated_hours =&gt; 1000000))    foobars(:thirty_day_foobar).save.should be_true  endend</pre>OK, I know I need to work on my RSpec writing skills but I hope you get the point. The behaviour example above doesn't need any implementation code to work. It just works, and it prevents the web developer from writing validation code that will prohibit tasks from being added to the Foobar. It looks fine to me, but it would be good to get some feedback.
