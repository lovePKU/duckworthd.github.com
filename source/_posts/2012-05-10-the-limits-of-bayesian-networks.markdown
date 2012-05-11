---
layout: post
title: "The Limits of Bayesian Networks"
date: 2012-05-10 20:03
comments: true
categories: 
---

  So you're a smart guy or gal.  You have Bayesian Networks down.  [Belief Propagation](http://en.wikipedia.org/wiki/Belief_propagation)?  "Please, I build Junction Trees in my sleep".  What's that, a [Dynamic Bayesian Network](http://www.cs.ubc.ca/~murphyk/Papers/dbnchapter.pdf)?  "I'll have a [Variational Inference](http://en.wikipedia.org/wiki/Variational_Bayesian_methods) algorithm done by the end of the week, and if that isn't enough I'll whip out my [Particle Filter](http://www.cs.ubc.ca/~arnaud/doucet_johansen_tutorialPF.pdf).  MCMC, EM, whatever, I got this.  Give me a Bayesian Network, and I'll show you how to do inference", you say.

  But believe it or not, Bayesian Networks just aren't enough, and even if you _do_ have inference in Bayesian Networks down cold, the world is far, far bigger than that.  Let's take a look at an example.

  A storm is coming, and it's about to hit town A.  Unfortunately, they're completely in the dark about it and have absolutely no idea.  Still, they're prepared as much as they always are ($$P_A$$), and when it finally comes, they sustain damage $$D_A$$ based on how much they prepared and how strong the storm was ($$S$$).  The storm then turns to town $$B$$, butseeing what happened to town A, they're more prepared (thus the edge $$D_A \rightarrow P_B$$).  The storm finally comes, and they take damage $$D_B$$.We can make a Bayesian Network for this generative model below.

{% img center /images/bayesian-networks-arent-enough/weather-prep-1.png %}

  So far so good, but what if we didn't know which town $$S$$ is going to hit first ($$F$$)?  Sure, we may think, "Well, if A was hit first, it's the same as before, but if B is hit first, we have $$D_B$$ pointing to $$P_A$$," but look at what happens,

{% img center /images/bayesian-networks-arent-enough/weather-prep-2.png %}

  Woah woah woah, a _loop_?  You just _know_ that's not allowed. We're able to provide a "generative story", so why can't we make a Bayesian Network for it?  The issue here is one of **identity uncertainty** and **context-specific independence**.  If we knew the identity of the city to be hit first, we know that one of the edges in the center don't matter.  Really, we can think of $$F$$ as selecting one of two possible Bayesian Networks which differ only by one edge.  
  
  At the same time, this is also a case of context-specific independence.  Given the "context" $$F=A$$, we know that $$P_A$$ is independent of $$D_B$$.  On the other hand, if $$F=B$$, then $$P_B$$ is independent of $$D_A$$.  Identity uncertainty and context-specific independence are really two sides to the same coin.

  So how can we do inference?  Monte Carlo algorithms luckily seem to "just work" (see Brian Milch's Thesis), but we don't know a whole lot about exact or Variational Methods.  
  
  There have been a number of projects over the last decade to make a "language" broader than Bayesian Networks to define generative models, including [MIT-Church](http://projects.csail.mit.edu/church/wiki/Church),  [Markov Logic Networks](http://alchemy.cs.washington.edu/), [IBAL](http://citeseer.ist.psu.edu/viewdoc/summary?doi=10.1.1.29.1299), and [BLOG](http://people.csail.mit.edu/milch/blog/index.html).  With any luck, we'll be teaching students how to use one of these in our undergraduate AI classes in a few years.
