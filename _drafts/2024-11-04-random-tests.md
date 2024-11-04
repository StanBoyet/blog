---
layout: post
title: "Stay away from randomness in your test-suites"
author: stan
tags: [ code ]
beforetoc: ""
toc: false
mermaid: false
excerpt: "About randomness in our test-suite and how even though it might appear to be a good idea it was far from that in my past experiences."
---

Our test-suite is the cornerstone of our CI/CD pipeline. We could talk about coverage (and how it might not matter that much in itself, more on that later?), about how you can shape your engineering culture around it (definitely more on that later) or even on how you could consider that it does not add complexity to a codebase, on the the contrary (more on that _now_ with [this talk](https://www.youtube.com/watch?v=-vuoKq7Ub6g) I gave in May 24' at an Odoo developer conference). Today, though, I want to talk about another aspect of our test-suite.

A smaller and more practical post about randomness and how even though it might appear to be a good idea it was far from that iWn my past experiences. Maybe it does not apply to your use-case, reader discretion advised.

But before telling you why I think it's a bad idea...

## After all, why not...?
![after_all]({{ site.baseurl }}/assets/images/posts/after_all.jpg)

I fell in this trope before: "Our users ended up in this obscure edge-case, they're so random lol" and believed in applying this straight to our test-suite:
> To better align with our users' behaviour, let's apply randomness. If our tests are random, they will behave just like our users.

Checkmate CI/CD, I win.

Another entry door to go full-random in our suite was a (vain) attempt at covering more ground. If `a = [true, false].sample` and I have a conditional on `a` down the road, well I'll cover more ground.

Checkmate CI/CD, I win, _again_.

Well, not so fast. As you've seen with the title and the lengthy intro, of course these were shortsighted and more the result of a lack of experience coupled with a lack of thoroughness in our approach.

## The forest hidden behind the random

My experience with introducing randomness myself was actually tyring to hide shortcomings I sometimes knew I had and most of the time did not know about.

About my first example, and users' allegeded randomness, it's a clue of a deeper issue: users are not random, they're just not behaving like you planned they would. And ~~maybe~~ they're not to blame for it. You have most likely have a classic case of mis-monitoring on your hands, or worst: a mis-alignment with what you delivered vs what they expected.

### Where do we start?

I'm not advocating to cover all edge-cases. It's both counter-productive and de-motivating for you and your team if you're getting started.

I think a healthy process looks like:
- Identify your happy path and cover it first
- Move to a basic "for basic issues, please fail gracefull". eg: logged out user trying to access a private resource gets a 404 and is redirected to login page
- Select a handful, maybe just one, of edge-case and test it. It'll serve as a template for the next ones

A word on the 


## What should we expect as a result?

- We want repeatability
    - CI/CD should fall for a reason, and that reason should not be random
        - → Insert the anecdote about the tests failing on thursday afternoon
- Edge cases should be covered
    - More work? Yes
    - But the day they break, you know it when it’s relevant: now, not in a week after 150th attempt
    - useful coverage, not soon-to-be-expired one
A wise man one told me: Just like in production software, aim to have failure blowing up in your face as soon as possible rather than most likely but later on a weekend

## Bonus: how to debug a randomly failing test-suite?

- How to debug?
    - Get the seed! Reproduce the failure and log the context
        - worst case scenario, if the `expect(result).to be_true` statement fails, log everything right above `puts "my logs" if result != true` above. You’ll “pollute” your code, but the moment a run fails, you’ll get those logs. Your turn to make them meaningful
    - I'm not proud, but I wrote a couple of times a `for i in {1..100}; do bundle exec rspec folder/feature_spec.rb; done`