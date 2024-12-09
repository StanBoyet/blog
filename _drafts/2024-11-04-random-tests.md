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

Our test-suite is the cornerstone of our CI/CD pipeline. We could talk about coverage (and how it might _not_ matter that much by itself, more on that later?), about how you can shape your engineering culture around it (_definitely_ more on that later) or even on how you could consider that it does not add complexity to a codebase, on the the contrary (more on that _now_ with [this talk](https://www.youtube.com/watch?v=-vuoKq7Ub6g) I gave in May 24' at an Odoo developer conference).

Today, though, I want to talk about another aspect of our test-suite. It'll be a smaller and more practical post about randomness and how even though it might appear to be a good idea, it did not quite deliver as I've expected in my past experiences. Maybe it does not apply to your use-case, maybe it does, reader discretion advised.

But before telling you why I think it's a bad idea...

## After all, why not...?
![after_all]({{ site.baseurl }}/assets/images/posts/after_all.jpg)

I fell in this trope before: "Our users ended up in this obscure edge-case, they're so random lol" and believed in applying this straight to our test-suite:
> To better align with our users' behaviour, let's apply randomness. If our tests are random, they will behave just like our users.

Checkmate CI/CD, I win.

Another entry door to go full-random in our suite was a (vain) attempt at covering more ground. If `a = [true, false].sample` and I have a conditional checking `a` down the road, well I'll cover more ground.

Checkmate CI/CD, I win, _again_.

Well, not so fast. As you've seen with the title and the lengthy intro, these were shortsighted and more the result of a lack of experience coupled with a lack of thoroughness in our approach.

## The forest hidden behind the random

My experience with introducing randomness myself was actually tyring to hide shortcomings I (sometimes) knew I had and (most of the time) did not know about.

About my first example and users' allegeded randomness, it's a clue of a deeper issue: users are not random, they're just not behaving like you planned they would. And ~~maybe~~ they're not to blame for it. You most likely have a classic case of mis-monitoring on your hands, or worst: a mis-alignment of what you delivered vs what they expected.

### Where do we start?

I'm not advocating to cover all edge-cases. It's both counter-productive and de-motivating for you and your team if you're getting started.

I think a healthy process looks like:
- Identify your happy path and cover it first
- Move to a basic "for basic issues, please fail gracefully". eg: logged out user trying to access a private resource gets a 404 and is redirected to login page
- Select a handful, maybe just one, of edge-case and test it. It'll serve as a template for the next ones. The goal is to smooth out the experience for future you (and/or team members) the next time there is an issue so you can have a semblance of TDD while resolving the issue.

## What should we expect as a result?

### We want repeatibility
In our CI/CD pipeline, we want _meaning_ behing a failure. Nothing more frustrating that a red test-suite following a change that has nothing to do with said-failure. It failure there is, I want and expect it to be related to what I just changed.

Not entirely related to randomness but very much related to repeatibility: we used to have a failing test-suite on thursday afternoons. Why? As you've guessed it: week-end was just around the corner and we were trying to compute work-week deadlines. During this half-day, our test data was skewed and we could not deploy on thursdays. Of course I'm exaggerating, we weren't stuck per se, but for the sake of the thought experiment, try to explain to your support team that on Thursday afternoons you can't deploy fixes because... it's thursday. (Please don't, at least invent something about [cosmic rays](https://en.wikipedia.org/wiki/Soft_error#Cosmic_rays_creating_energetic_neutrons_and_protons))

### Cover those edge-cases
As stated before, I would not advise to start with that, but it should be the objective you have in mind. Why? Because the day the pipeline breaks, it's for a good reason. You know the failure is relevant _now_. Not on thursday afternoons or in 150 attempts.
As a result, your code-coverage becomes useful all of a sudden, it's not about to get expired in a couple of weeks. If it fails, it fails now. 
A somewhat [wise man](https://www.linkedin.com/in/philippevaneerdenbrugghe/) once told me:
> Just like in production software, aim to have failure blowing up in your face as soon as possible rather than "most likely" but later on a weekend

## Bonus: how to debug a randomly failing test-suite?

### Seed
When running your tests, you most likely have a seed with it by default, just like this when running `rspec`:
```bash
rspec spec

Randomized with seed 48111
.....*.........
```

The output gives you the randomly selected seed which you can use later on like this:
```bash
rspec spec --seed 48111
```

Why? Maybe the test is failing because it actually alters the DB in a way that the following case was not ready for or whatnot. It's usually the first approach I take, to make sure I'm in the exact same conditions as the ones that led to a failure.

### Log everything
Worst case scenario, if the `expect(result).to be_true` statement fails, log everything right above like this: `puts "my logs" if result != true`. 
You’ll “pollute” your code, but the moment a run fails, you’ll get those logs. Your turn to make them meaningful. This can be used in combination with the last trick:

### Brute-force
I'm not proud, but I wrote a couple of times a `for i in {1..100}; do bundle exec rspec folder/feature_spec.rb; done`. This is some last-resort-end-of-week kind of statement. Most of the time it's right before leaving for lunch or something, and I come back to a screen full of logs (see previous point). I think this helped me once or twice to actually get something out of it. 

> It ain't stupid if it works

But this is still pretty stupid.