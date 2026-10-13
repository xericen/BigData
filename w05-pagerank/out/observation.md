# Week 05 observations

## Task 1
The broken walk loses rank when it reaches a dead end; I modelled each dead end as linking uniformly to every node, which conserves that mass.
Uniform teleportation also lets the surfer escape a spider trap, so the same global redistribution prevents both permanent loss and permanent capture.
Beta is the probability of following a link; with probability `1 - beta`, the surfer jumps to a uniformly selected page.

## Task 2
Iterations rose from 14 at beta 0.50 to 24 at 0.99 because weaker teleportation mixes the walk more slowly as beta approaches 1.
At beta 0.85, a 5x larger graph needed only 2 more iterations but about 7.7x the wall time: convergence rate is mathematical, while every iteration must process more nodes and edges.
The top-10 membership stayed fixed, but its order first changed at beta 0.70, so a published ranking should disclose beta even when its leading set looks stable.

## Task 3
I retained the input adjacency lists and two length-n rank vectors instead of an n-squared transition matrix, using O(n) extra floats while traversing O(E) stored links.
Teleportation and dead-end mass are each one uniform scalar: compute it once per iteration, then use it to initialise every next-rank entry without a dense matrix.
The worst difference from the dense answer was `1.18e-15`, caused only by floating-point additions occurring in a different order.
