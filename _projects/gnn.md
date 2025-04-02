---
layout: page
title: Graph Neural Networks
description: A primer on Graph Neural Networks
img:
importance: 2
category: fun
giscus_comments: false

---

Traditional Neural Networks cannot handle graph structured data at all. They tend to completely ignore topological information, which can be invaluable since graphs can be used to model relationships across most domains. Graph Neural Networks provide a way to model these relationships. Geometric Deep Learning also looks at GNNs as a generalisation of traditional neural networks.

## Limitations of Traditional Neural networks

The two major challenges with using traditional neural networks for graph structured data are size and invariance

#### Size

Neural Networks need inputs to be of a fixed size. For input modalities such as images, one can up/downsample images that are too small/large. With graphs, the number of nodes will often not be the same across all points in a dataset with no canonical way of adding/removing nodes without changing the graph itself. Additionally, the topological structure of a graph is much more complicated than for modalities such as text. 

#### Invariance

Graphs are the same no matter how you permute them. Our understanding of the graph isomorphism problem (determining whether two graphs are identical) does not even tell us how difficult the problem is. This can introduce redundancy into the data, and if the architecture does not account for this invariance the network may be unable to learn patterns. 

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/graph_permutation.svg" title="graph_permutation" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/original_image.png" title="original_image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/permuted_image.png" title="permuted_image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Permuting an image destroys the grid structure, changing it into a completely different image.
</div>

## Message Passing

One class of operations that meets the above criteria is message passing. Given a graph $$G = (V, E)$$, the update function for the network is

\[h^{k+1}(v) = f(h^k(v), g(\{h^k(n): n \in N(v)\}))\]

The above equation applies $$g$$ to the hidden states of neighbours of node $$v$$, denoted by $$(N(v))$$, and then applies $$f$$ to the result and the hidden state of $$g$$. Common choices for $$g$$ include summation, component wise products, mean and max, while $$f$$ can be a linear transformation, a non-linear transformation or a full neural network. Updates are usually calculated using a linear transformation followed by a nonlinear activation as it is for MLPs. 

The diagram below shows a single round of message passing. The nodes each have 3 features, being filled in with the RGB values corresponding to their colours. The update function is $$h^{k+1}(v) = f(h^k(v), g(\{h^k(n): n \in N(v)\})) = \frac{1}{\#(N(v))}\sum_{p \in N(v)} h^k(p)$$, which computes the mean feature vector across all the neighbourhood of the node.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/gnn_initial.svg" title="initial_state" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A simple graph with 5 nodes.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/gnn_message.svg" title="message_creation" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="caption">
    Aggregation across the neighbourhoods of each node.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/gnn_final.svg" title="final_state" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Updating using neighbourhood mean.
</div>


## Graph Prediction Tasks

Graph prediction tasks can be done at the node, edge or graph level

Node Prediction: The task here is to predict a property of a node from a node embedding. e.g. predicting the traffic on a new website using information from the websites that link to it

Edge Prediction: The task here is to predict a property of an edge from an edge embedding. e.g. predicting the type of relationships between 2 nodes on a knowledge graph or making a recommendation based on past consumption history

Graph prediction: The task here is to predict a property of a whole graph, usually after aggregation of it's components to get a single embedding. e.g. predicting the melting point of a molecule or classifying a network based on its nodes and connection pattern

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/graph_tasks.png" title="message_creation" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="caption">
    Different types of graph tasks. Credits: (<a href="https://towardsdatascience.com/graph-machine-learning-an-overview-c996e53fab90/" target="_blank"> Zach Blumenfield </a>)
</div>


## Smoothing and Squashing

Passing data through each layer of a GNN increases how far away information is being aggregated from for each node. After $$n$$ passes, the representation of the node has information from all nodes which can be reached in at most $$n$$ steps. If the same is true for every single node on the graph, the network may lose valuable node-level information because each of their representations are 'smoothed' across the entire graph. Graph properties like the diameter and centrality thus become critical design considerations when tuning hyperparameters. 

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/gnn_layers.png" title="message_creation" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="caption">
    Layers of a GNN and which nodes contribute after a certain number of layers. Credits: (<a href="https://medium.com/data-science/do-we-need-deep-graph-neural-networks-be62d3ec5c59" target="_blank"> Michael Bronstein </a>)
</div>


Squashing occurs for problems that require interactions from nodes that are far away from each other. Properties of certain chemicals depend on the interaction between molecules that are at opposite ends. If there happens to be 'hub' element, that connects different parts of the molecule together, a large amount of information is being squashed into a single node leading to a catastrophic information loss. This was introduced by Alon and Yahav in 2021 and remains a problem of interest.

