---
layout: post
title: "Bias v. variance in theoretical analysis"
date: 2016-04-22 00:00:00
categories: [blog]
tags: Metis, thoughts
excerpt: "Or, how I ~~stopped worrying~~ and learned to ~~love~~ prediction...Er..."
---

The last couple of weeks I've been having a bit of a crisis of faith. 

For the most part, my background as a former political scientist with some graduate training in statistics has been extremely useful. To some extent, though, the most interesting (and probably most important) thing that I'm trying to learn -- how to transition from sort of academic data use and manipulation to practical data science -- is also the most challenging. (Philosophically, anyway. For 'most mentally challenging' please see 'what on earth is Bayesian time series' and 'why do I keep forgetting to rename just that one variable every time'.)

Let's back up for a second. One of the main reasons I left academia is that I became less and less convinced that the current methods in use are ill-suited to capturing anything about the world. There's a push toward more math-y, science-y 'hardening' of the field (in the sense of moving away from being a 'soft' social science) that is unfortunately at odds with studying the type of phenomena that make the field sort of substantively interesting. Real-world events, particularly the ones we would love to explain, such as war, regime change, state formation, etc., are rare, self-selecting events that we can only observe, and they are messy processes for which the inputs are often hard to identify and measure.

The dream underlying political science as a discipline is a seductive one, I think. In college, I had lofty ideals of discovering *just one small true thing* about the world, of adding to the cumulative store of human knowledge. I'm now much less hubristic and much more skeptical of that notion, but I think I would still be sympathetic to the search if the process hadn't become nearly pathological due to the peculiarities of academia. 

<s>Related: it is with a certain amount of chagrin that I often find insights from political science (oten lauded as 'surprising and yet not counter-intuitive' and having required a very large and sophisticated amount of work to uncover) casually mentioned as a widely understood insight or conventional wisdom in novels and newspaper articles.</s>

---

> "(a) we don’t know enough at this point to do this exactly right and (b) in the future, what it’s taking me seven years to do with a mathematical model is going to take a computer hours, or seconds.” -- [Irene Pepperberg](http://www.newyorker.com/magazine/2008/05/12/birdbrain)

Just read this tonight while looking up Alex videos and was struck by how I feel the same way about a completely different field (Dr. Pepperberg was in theoretical chemistry). In particular, I think about all of those papers and books published in the 1990s that have simple regression models rife with endogeneity and that are basically worthless today. To that extent, I think qualitative stuff can hold more longterm value, even if it turns out to be "wrong."

---

In my Metis interview with Laurie Skelly (a very cool lady), we commiserated about academic research essentially being pushed into impracticality by the stringent requirements of making causal claims. If I want to *explain* certain phenomena
