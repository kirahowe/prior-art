---
date: 2021-02-09
slug: articles/identity-as-value
title: Think of a thing's identity as it's value, not it's container
site/tags:
  - identity
  - values
---

Writing code that works is hard. I've been exploring things that make software complex in an attempt to learn how to write better code. I guess "better" is subjective, but in this case I mean "easier to change" and "with fewer bugs". [State](/articles/how-state-makes-software-complex/) is one big factor that makes software complex. Most code that does anything useful needs state, though.

How we model that state can make a big difference in how easy or hard it is to understand code. It really comes down to reference vs. value equality semantics, but those are abstract terms and ways of thinking about things that nobody really talks about in real life.

It's easy to gloss over this topic in a conversation about computery things, but it's one that's really worth thinking about a bit because it can have a big impact on our ability to write code that works.

## Reference semantics

In most of the popular languages, each object is a unique, distinct thing, regardless of its current state. Take the example of dates in JavaScript:

```javascript
> date = new Date()                // Thu Jan 28 2021 21:54:34 GMT-0500 (EST)
> date.setDate(date.getDate() + 1) // 1611975274726
> date                             // Fri Jan 29 2021 21:54:34 GMT-0500 (EST)
> date === date                    // true
```

We make a new date, then change the internal state of that date, setting its current value to tomorrow, yet we still say it's the same date.

The thing is, it's not the same date. January 28th _is not the same_ as January 29th, but the way we describe objects in most programming languages forces us to wrap our heads around this unnatural way of thinking about what something is.

The flip side is equally confusing -- when you look at things that seem like they should be equal but aren't. Like in JavaScript again:

```javascript
[1, 2, 3] === [1, 2, 3] // false
```

You can see that  `[1, 2, 3]` is actually the same as `[1, 2, 3]`, and there are a lot of situations where it would be convenient to be able to check that programatically, but in most languages they're not actually equivalent, because each thing (array of 1, 2, 3, in this case) is a unique entity. The _identity_ of a thing is defined as the container, the object that holds all the attributes. The current value of those attributes does not affect an object's identity in these languages.

## Re-inventing equality

Thinking of identity this way forces us to re-invent what it means for things to be equal. In languages that refer to a container of attributes as a thing's identity, we have to re-define what equality means in some domain-specific sense for each new type of object. In order to check whether two obejcts are equal, you have to write extra code to check whether the _current value_ of their attributes are equal. That can add up to a lot of extra code! 

It also adds the issue of having to think about time to our code, because in this world whether two things are equal or not depends on the values of their attributes _at the instant_ you check, but that could change. To check whether two things are equal you have to effectively pause the universe and grab a snapshot. All of this adds unnecessary complexity to code. It's unnecessary because it's imposed by the language, not a consequence of logical constraints.

## Unwrap your data and think in values instead

We can get rid of this entire category of problem by thinking about a thing's identity as it's _value_ directly, intead of wrapping it in a container and calling that the identity. There are a lot of languages where the equivalent of `[1, 2, 3] === [1, 2, 3]` _is_ true, as we can intuitively see that it is.

Working with data directly like this is much easier to reason about and removes whole categories of problems to free up your mental bandwidth to work on your actual app, not problems introduced by your language. 

You never have to care again about what fields to compare to see if things are equal. This also solves the time problem, because values are inherently immutable. 2 is always 2, it will never be different no matter how much later you check. This means you no longer have to care about _when_ objects change in your code -- if you unwrap the objects and just work with values directly, it suddenly becomes possible to zero in on one tiny piece of your code and stop caring about how all the rest of it might affect the value you're currently trying to do something with.  

## Reference semantics add unnecessary complexity

The biggest win with writing code this way -- using naked, immutable data structures -- is that it reduces the complexity of your whole codebase. The value of a given thing is always transparent, and never changes over time. This removes whole classes of complexity that we can just stop caring about. You never have to worry again about what other methods might sneakily change the thing you're working with. And you don't have to care about _when_ things happen anymore, either. You can always know that whatever value you're dealing with is static for the purposes of your current context.

Clojure is one language that is designed like this by default, but it's possble to write code this way in many languages. Give it a try and see what you think!