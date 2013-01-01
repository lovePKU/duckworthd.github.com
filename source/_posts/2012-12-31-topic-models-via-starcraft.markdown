---
layout: post
title: "Topic Models via Starcraft"
date: 2012-12-31 20:33
comments: true
categories: 
---

Today, I'd like to explain to you how Topic Models work, and I will do so via a running Starcraft example.  How are these two related? Well let's find out!


Step 1: Mixture Models
----------------------


Before we jump into full-on Topic Models, let's start with the basic Mixture Model.  Imagine a High Templar commanding a battalion of Dark Templar (for the glory of Aiur!) tasked with invading an unsuspecting Terran planet.  Luckily, this is not the first time the Protoss have visited this planet, and Pylons are already in place, meaning his troops need only teleport in.  However, these Pylons were placed thousands of years ago, and while they are still in working order, attempting to teleport a unit to one results in them being placed in the vicinity of the Pylon, rather than right next to it.  Some Pylons are in a better state of repair than others, so depending on which Pylon is used, he may have a better chance of putting his Dark Templar where he intends.  In addition, some Pylons are closer to key Terran infrastructure than others, so simply choosing the Pylon in the best state of repair isn't the best idea.


The High Templar devises a plan to surprise the Terran planet by grouping his Dark Templar troops into squads and teleporting each squad to the same Pylon.  Being cloaked, however, only Dark Templar at the same Pylon will be able to see and communicate with each other.  To make the best use of his forces, he divvies up his troops into squads of the appropriate size and begins the teleportation sequence.  After that, they are on their own.


Meanwhile, a traitorous Terran-friendly Archon watches silently from his ship orbiting the planet.  Telepathically he senses the location of each Dark Templar and of the re-activated Pylons, but is unable to sense which squad each Dark Templar belongs to or the state of each Pylon.  To best help his Terran allies, he must identify both.  Were he to know which squad each Dark Templar belongs to, he would be able to estimate the functionality of each Pylon.  On the other hand, if he knew how well each Pylon were working, then he would be better able to guess which Dark Templar came from which Pylon.


The problem of seeing data points (Dark Templar positions) that you _know_ belong to individual clusters (squads) but not being able to directly observe which cluster (squad) each data point (unit position) belongs to is precisely what the Mixture Model assigns probabilities to.  Another way of putting it is that the High Templar selects one of his Dark Templar subordinates uniformly at random, chooses which Pylon to assign him to randomly with probability proportional to what percentage of his troops he wants to allocate there, then when the Dark Templar is teleported, his location is randomly sampled based on the strength of the Pylon he is using.  We can write the probability of the placement and squad assignments of all units as follows,


$$ P(p_{1:n_{unit}}, s_{1:n_{unit}}, \theta_{1:k} | w_{1:k}) = \prod_{j=1}^{k} P(\theta_j) \prod_{i=1}^{n} P(s_i | w_{1:k}) P(p_i | \theta_{s_i}) $$

<table>
  <tr>
    <td> $p_i$ </td>
    <td> position of Dark Templar $i$ </td>
  </tr>
  <tr>
    <td> $s_i$ </td>
    <td> squad number of Dark Templar $i$ </td>
  </tr>
  <tr> 
    <td> $\theta_j$ </td>
    <td> parameters for probability distribution of position of a Dark Templar given he is assigned to Pylon $j$ </td>
  </tr>
  <tr>
    <td> $w_{j}$ </td>
    <td> probability of sending a Dark Templar to Pylon $j$ </td>
  </tr>
  <tr>
    <td> $n_{unit}$ </td>
    <td> number of Dark Templar </td>
  </tr>
  <tr>
    <td> $k$ </td>
    <td> number of Pylons </td>
  </tr>
</table>


Once we assume the above probability distribution, we can use one of a variety of algorithm for Probabilistic Inference to find $P(s_{1:n}, \theta_{1:k} | p_{1:n})$, giving us a good guess as to which cluster each data point belongs to and how strong each Pylon is.


Step 2: Topic Models
--------------------


Back to our example, let's now extend the Mixture Model to a full Topic Model.  Suppose now that the traitorous Archon is not just any Archon, but a Dark Archon gifted with the ability to observe the outcome of many alternative universes.  In each alternative universe, the High Templar chooses different proportions of his units to assign to each squad and each unit's position ends up being different, but the behavior of each Pylon remains unchanged.  The Dark Archon's power is limited however, and he can only observe a finite number of different universes before exhausting his energy.  While the outcome of each alternative universe cannot help him figure out which Dark Templar is assigned to which Pylon directly in this universe, it can help him figure out how each Pylon behaves.  In addition, the Dark Archon gets a better idea of how the opposing High Templar will assign his Dark Templar as the strategic value of each Pylon remains the same.


The problem I have just described is precisely that modeled by Latent Dirichlet Allocation, the most basic Probabilistic Topic Model. Each universe is that of a single "document", with its own unobserved document-cluster weights (proportions).  Within each universe, there exists a Mixture Model, but with a twist -- all universes (documents) share the same cluster (Pylon) parameters.  To put it mathematically,


$$ P(p_{1:n_{doc},1:n_{unit}}, s_{1:n_{doc},1:n_{unit}}, w_{1:n_{doc}, 1:k}, \theta_{1:k}) $$
$$ = P(\alpha) \prod_{j=1}^{k} P(\theta_{j}) \prod_{l=1}^{n_{doc}} P(w_{l, 1:k} | \alpha) \prod_{i=1}^{n_{unit}} P(s_{l,i} | w_{l, 1:k}) P(p_{l,i} | \theta_{s_{l,i}}) $$

<table>
  <tr>
     <td> $p_{l,i}$ </td>
     <td> position of $i$-th Dark Templar in $l$-th universe </td>
  </tr>
  <tr>
     <td> $s_{l,i}$ </td>
     <td> squad number of $i$-th Dark Templar in $l$-th universe </td>
  </tr>
  <tr>
     <td> $w_{l,j}$ </td>
     <td> probability of sending a Dark Templar to Pylon $j$ in universe $l$ </td>
  </tr>
  <tr>
     <td> $\theta_{j}$ </td>
     <td> parameters for probability distribution of position of a Dark Templar given he is assigned to Pylon $j$ </td>
  </tr>
  <tr>
     <td> $\alpha$ </td>
     <td> parameters for probability distribution over universe-specific proportions </td>
  </tr>
  <tr>
     <td> $n_{doc}$ </td>
     <td> number of universes </td>
  </tr>
  <tr>
     <td> $n_{unit}$ </td>
     <td> number of Dark Templar units </td>
  </tr>
  <tr>
     <td> $k$ </td>
     <td> number of Pylons </td>
  </tr>
</table>



Once again, once we assume the above probability distribution, we can use Probabilistic Inference to find a good estimate for the parameters common to every universe -- $\alpha$ and $\theta_{1:k}$.


Epilogue
--------


To summarize, Topic Models are simply a probabilistic model assigning probabilities to multiple-universe Mixture Models.  So how do  "topics" fit into all of this?  Recall earlier that I described each universe as a "document" -- there's a good reason for this. In the original Latent Dirichlet Allocation paper, the goal wasn't to figure out which squads Dark Templars are assigned to but rather which "topics" individual words in articles were part of.  The intuition is that when you're talking about a particular topic (e.g. sports, wallabees, or Cards Against Humanity), there is a distinctive probability distribution over words you would use, separate from every other topic.  Keep in mind that nothing about the model includes human supervision -- what the model thinks is a likely set of "topics" may not correspond to anything we as readers identify as meaningful.  However, in practice some rather interesting "topics" pop up, so a cottage research field has spent the last 10+ years trying to extend Latent Dirichlet Allocation to better capture the "topics" we as humans find "nice".


References
----------

* Latent Dirichlet Allocation

























