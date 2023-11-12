---
layout: page
title: "REST2/FF-REMD algorithm"
permalink: /rest2-ff_remd/
footer: true
project: true
---

Here I cover a Replica Exchange with Solute Tempering and Force Field Replica Exchange
Molecular Dynamics (REST2/FF-REMD) algorithm _concept_ 
that can be implemented in NAMD through
the 
[multiple-copy scripting interface](http://www.ks.uiuc.edu/Research/namd/2.14/ug/node9.html#SECTION00052300000000000000).

Replica Exchange Solute Tempering (REST2) is an enhanced sampling technique that
was published by [Wang et al.](https://pubs.acs.org/doi/10.1021/jp204407d) 
The traditional Temperature Replica Exchange Molecular Dyanmics (TREMD) requires many replicas
to have decent exchanges between copies because good overlap at the tails of the distribution 
are required for exchanges to occur. In contrast, REST2 does not require many
replicas.

In REST2 the protein-protein, protein-water, and water-water interactions are scaled
by $$ \beta $$, 

$$ U^{SS}(\vec{r}) = \beta U_{pp}( \vec{r} ) + \sqrt{\beta} U_{pw}(\vec{r}) 
   + U_{ww}(\vec{r}) $$

where $$ U_{pp} $$ denote the protein-protein interactions, $$ U_{pw} $$ denote the 
protein-water interactions, and $$ U_{ww} $$ denote the water-water interactions.
Thus, REST2 allows sampling at the desired temperature by scaling the intramolecular
potential energy of the protein and the intermolecular interactions between protein-water and
water-water.

The algorithm follows this ladder/linear convention:

{% include image.html file="/assets/new_algorithm.png" %}

where specifically 
the coordinates are swaped between copies.
There are $$ N $$ unbiased force field replicas
and $$ n $$ $$ \beta $$-scaled REST2 replicas for a total of $$ N+n $$
replicas/copies.
The depiction of the algorithm can be viewed as a ladder. 
Where coordinate swaps are performed periodically at some frequency
between the $$ N $$ unbiased force field replicas and the $$ \beta $$-scaled REST2 replicas.
Thus, the unbiased replicas swap with the biased REST2 replicas allowing enhanced sampling 
when you have many force fields to be simulated. The force fields themselves could be
composed of perturbed protein-protein, protein-ion, and protein-water interactions.
Finally, the algorithm is coded like so in Tcl:

{% highlight ruby %}
# Check if a swap should be attempted at current step for replica i
if { (!($i_step % $ff_frequency) && $i_run && !($replica_id)) || \ 
     ($num_temp == 1 && !($replica_id)) } {
    # Coordinate Swap i.e., Force field swap
    set swap [expr int($num_forcefields * rand() + 2)] ; # Swapping force field chosen at random
    # Swap i with j
    for { set i 1 } { $i < $num_replicas } { incr i } {
        replicaSend $swap $i
    }
# Check if a swap should be attempted at current step for replica j
} elseif { (!($i_step % $ff_frequency) && $i_run && $replica_id) \
           || ($num_temp == 1 && $replica_id) } {
    # Swap j with i
    set swap [replicaRecv 0]
}

# Finally check swapping criterion (for example, Monte Carlo Exchange Criterion)
{% endhighlight %}

