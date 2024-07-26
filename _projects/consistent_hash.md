---
layout: page
title: Consistent Hash
description: Final Project for Data Structures
img: 
importance: 1
category: work
related_publications: False
---

For my final project in my Data Structures, I built a consistently hashed, self-balancing (AVL) tree. This is a data structure commonly used in load balancing for distributed services.

Say you are running a games website and you have multiple servers all over the world, since you have too many users to be able to handle each of them on any single server.

A simple solution one might think of is just using a hash table, and route the requests using the residuals mod n. 

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="hashtable_use" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    An illustration of using a simple hash table to decide which server should handle a request. 
</div>

Now, consider the case where server k goes down. The simplest solution would be to route the requests mod n - 1. However, this would not just effect the requests that would have gone to server k, but to every other server as well. This can cause major pandemonium by routing requests all over the place, especially if there are a large number of servers of which even a small number are expected to break down, as every breakdown will cause all the requests to be routed to completely different servers. 

Solution

A particular solution to this problem is consistent hashing. The easiest way to visualise this is using a circle, and selecting n points on the circle which are approximately evenly distributed around it's surface. The requests can then be hashed onto the circumference of the circle, and the first server in the clockwise(anticlockwise) direction is assigned to the user. When server $$k$$ goes down, the server $$k + 1$$, which is the next server in the clockwise(anticlockwise) direction. 

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="hashtable_use" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    An illustration of using a simple hash table to decide which server should handle a request. 
</div>

I implemented this using a self-balancing binary search tree

Analysing The Solution

Retrieving a server: $$O(\log(n))$$