---
layout: page
title: Consistent Hash
description: Final Project for Data Structures
img:
importance: 7
category: fun
related_publications: False
---

For my final project in my Data Structures, I built a consistently hashed, self-balancing (AVL) tree. This is a data structure commonly used in load balancing for distributed services.

Say you are running a games website and you have multiple servers all over the world, since you have too many users to be able to handle each of them on any single server.

A simple solution one might think of is just using a hash table, and route the requests using the residuals mod n.

Now, consider the case where server k goes down. The simplest solution would be to route the requests mod n - 1. However, this would not just effect the requests that would have gone to server k, but to every other server as well. This can cause major pandemonium by routing requests all over the place, especially if there are a large number of servers of which even a small number are expected to break down, as every breakdown will cause all the requests to be routed to completely different servers.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/regular_hash.jpg" title="hashtable_use" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    An illustration of a server going down when using a hash table for load balancing (<a href="https://highscalability.com/consistent-hashing-algorithm" target="_blank">image credits</a>)
</div>

## Solution

A solution to this problem is consistent hashing. The easiest way to visualise this is using a circle, and selecting n points on the circle which are approximately evenly distributed around it's surface. The requests can then be hashed onto the circumference of the circle, and the first server in the clockwise(anticlockwise) direction is assigned to the user. When server $$k$$ goes down, the server $$k + 1$$, which is the next server in the clockwise(anticlockwise) direction.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/consistent_hash.jpg" title="consistent_hash_use" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    An illustration of a server going down when using a consistent hash for load balancing (<a href="highscalability.com/consistent-hashing-algorithm" target="_blank">image credits</a>)
</div>

I implemented this using a self-balancing binary search tree, with each node being a linked list storing the keys mapped to it. This allowed for efficient look-ups without a large delete cost.

#### Analysing The Solution

Adding or removing a server: $$O(\frac{K}{N} + \log(N))$$ compared to $$O(K)$$

Adding, removing or a server: $$O(\log(N))$$, compared to $$O(1)$$

When load balancing the former set of operations is much more expensive, since $$K >> N$$. This justifies the trade - off for this particular problem. [Rendezvous hashing](https://en.wikipedia.org/wiki/Rendezvous_hashing) is a related generalisation of this idea.
