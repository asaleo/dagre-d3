# dagre - Directed graph rendering

[![Build Status](https://secure.travis-ci.org/cpettitt/dagre.png)](http://travis-ci.org/cpettitt/dagre)

[![browser support](https://ci.testling.com/cpettitt/dagre.png)](https://ci.testling.com/cpettitt/dagre)

Dagre is a JavaScript library that makes it easy to lay out directed graphs on
the client-side.

Key priorities for this library are:

1. **Completely client-side computed layout**. There are great, feature-rich
   alternatives, like [graphviz](http://www.graphviz.org), if client-side
   layout is not a requirement for you.

2. **Speed**. Dagre must be able to draw medium sized graphs quickly, potentially
   at the cost of not being able to adopt more optimal or exact algorithms.

3. **Rendering agnostic**. Dagre requires only very basic information to lay out
   graphs, such as the dimensions of nodes. You're free to render the graph using
   whatever technology you prefer. We use [D3][]
   in some of our examples and highly recommend it if you plan to render using
   CSS and SVG.

Note that dagre is current a pre-1.0.0 library. We will do our best to
maintain backwards compatibility for patch level increases (e.g. 0.0.1 to
0.0.2) but make no claim to backwards compatibility across minor releases (e.g.
0.0.1 to 0.1.0). Watch our [CHANGELOG](CHANGELOG.md) for details on changes.

## Demo

Try our [interactive demo](http://cpettitt.github.com/project/dagre/latest/demo/demo.html)!

Or see an example of rendering the [TCP state diagram](http://cpettitt.github.com/project/dagre/latest/demo/demo-d3.html).

These demos and more can be found in the `demo` folder of the project. Simply
open them in your browser - there is no need to start a web server.

## Getting Dagre

### Browser Scrips

You can get the latest browser-ready scripts:

* [dagre.js](http://cpettitt.github.io/project/dagre/latest/dagre.js)
* [dagre.min.js](http://cpettitt.github.io/project/dagre/latest/dagre.min.js)

### NPM Install

Before installing this library you need to install the [npm package manager].

To get graphlib from npm, use:

    $ npm install graphlib

### Build From Source

Before building this library you need to install the [npm package manager].

Check out this project and run this command from the root of the project:

    $ make

This will generate `dagre.js` and `dagre.min.js` in the `dist` directory
of the project.

## Using Dagre

### A Note on Rendering

As mentioned above, dagre's focus in on graph layout only. This means that you
need something to actually render the graphs with the layout information from
dagre.

For a simple starting point, we suggest looking at the user contributed
`demo/dagre-d3-simple.js` script for doing good but easy rendering. To get
started look at `demo/sentence-tokenization.html`.

Another option is to use [JointJS][]. See the link for an article about how to
use it with Dagre. The ability to manipulate nodes and edges after they are
layed out and renderer can be very useful!

For even more control over rendering you can write a custom renderer or take
advantage or a library like [D3][], which makes it easier to creates graphs
with SVG. The code in `demo/dagre-d3-simple.js` uses D3, so this may be a
good place to start.

Ultimately we plan to make available some basic renderers for Dagre in the
near future.

### An Example Layout

First we need to load the dagre library. In an HTML page you do this by adding
the following snippet:

```html
<script src="http://PATH/TO/dagre.min.js"></script>
```

In node.js you use:

```js
var dagre = require("dagre");
```


The current version of Dagre takes an array of nodes and edges, along with some
optional configuration, and attaches layout information to the nodes and edges
in the `dagre` property.

A node must be an object with the following properties:

* `width` - how wide the node should be in pixels
* `height` - how tall the node should be in pixels

The attributes would typically come from a rendering engine that has already
determined the space needed for a node.

An edge must be an object with either references to its adjacent nodes:

* `source` - a reference to the source node
* `target` - a reference to the target node

Or with the ids of its adjacent nodes:

* `sourceId` - the id of the source node
* `targetId` - the id of the target node

Here's a quick example of how to set up nodes and edges:

```js
var nodes = [
    { id: "kspacey",   label: "Kevin Spacey",  width: 144, height: 100 },
    { id: "swilliams", label: "Saul Williams", width: 168, height: 100 },
    { id: "bpitt",     label: "Brad Pitt",     width: 108, height: 100 },
    { id: "hford",     label: "Harrison Ford", width: 168, height: 100 },
    { id: "oplat",     label: "Oliver Platt",  width: 144, height: 100 },
    { id: "kbacon",    label: "Kevin Bacon",   width: 121, height: 100 },

];
var edges = [
    { sourceId: "kspacey",   targetId: "swilliams" },
    { sourceId: "swilliams", targetId: "kbacon" },
    { sourceId: "bpitt",     targetId: "kbacon" },
    { sourceId: "hford",     targetId: "oplat" },
    { sourceId: "oplat",     targetId: "kbacon" }
];
```

Next we can ask dagre to do the layout for these nodes and edges. This is done
with the following code:

```js
dagre.layout()
     .nodes(nodes)
     .edges(edges)
     .run();
```

An object with layout information will be attached to each node and edge under
the `dagre` property.

The node's `dagre` object has the following properties:

* **x** - the x-coordinate of the center of the node
* **y** - the y-coordinate of the center of the node

The edge's `dagre` object has a `points` property, which is an array of objects
with the following properties:

* **x** - the x-coordinate for the center of this bend in the edge
* **y** - the y-coordinate for the center of this bend in the edge

For example, the following layout information is generated for the above objects:

```js
nodes.forEach(function(u) {
    console.log("Node " + u.dagre.id + ": " + JSON.stringify(u.dagre));
});
edges.forEach(function(e) {
    console.log("Edge " + e.sourceId + " -> " + e.targetId + ": " +
                JSON.stringify(e.dagre));
});
```

Prints:

```
Node kspacey: {"id":"kspacey","width":144,"height":100,"rank":0,"order":0,"ul":0,"ur":0,"dl":0,"dr":0,"x":84,"y":50}
Node swilliams: {"id":"swilliams","width":168,"height":100,"rank":2,"order":0,"ul":0,"ur":0,"dl":0,"dr":0,"x":84,"y":180}
Node bpitt: {"id":"bpitt","width":108,"height":100,"rank":2,"order":1,"ul":188,"ur":188,"dl":188,"dr":188,"x":272,"y":180}
Node hford: {"id":"hford","width":168,"height":100,"rank":0,"order":1,"ul":364,"ur":364,"dl":364,"dr":364,"x":448,"y":50}
Node oplat: {"id":"oplat","width":144,"height":100,"rank":2,"order":2,"ul":364,"ur":364,"dl":364,"dr":364,"x":448,"y":180}
Node kbacon: {"id":"kbacon","width":121,"height":100,"rank":4,"order":0,"ul":188,"ur":188,"dl":0,"dr":364,"x":272,"y":310}

Edge kspacey -> swilliams: {"points":[{"x":84,"y":115,"ul":0,"ur":0,"dl":0,"dr":0}],"id":"_E0","minLen":2,"width":0,"height":0}
Edge swilliams -> kbacon: {"points":[{"x":84,"y":245,"ul":0,"ur":0,"dl":0,"dr":0}],"id":"_E1","minLen":2,"width":0,"height":0}
Edge bpitt -> kbacon: {"points":[{"x":272,"y":245,"ul":188,"ur":188,"dl":188,"dr":188}],"id":"_E2","minLen":2,"width":0,"height":0}
Edge hford -> oplat: {"points":[{"x":448,"y":115,"ul":364,"ur":364,"dl":364,"dr":364}],"id":"_E3","minLen":2,"width":0,"height":0}
Edge oplat -> kbacon: {"points":[{"x":448,"y":245,"ul":364,"ur":364,"dl":364,"dr":364}],"id":"_E4","minLen":2,"width":0,"height":0}
```

Besides just the `x` and `y` coordinates there are other debug attributes that
are not guaranteed to be present.

## Resources

* [Issue tracker](https://github.com/cpettitt/dagre/issues)
* [Mailing list](https://groups.google.com/group/dagre)

### Recommend Reading

This work was produced by taking advantage of many papers and books. If you're
interested in how dagre works internally here are some of the most important
papers to read.

The general skeleton for Dagre comes from *Gansner, et al., "A Technique for
Drawing Directed Graphs"*, which gives both an excellent high level overview of
the phases involved in layered drawing as well as diving into the details and
problems of each of the phases. Besides the basic skeleton, we specifically
used the technique described in the paper to produce an acyclic graph, and we
use the idea of a minimum spanning tree for ranking.  We do not currently use
the network simplex algorithm for ranking. If there is one paper to start with
when learning about layered graph drawing, this seems to be it!

For crossing minimization we used *Jünger and Mutzel, "2-Layer Straightline
Crossing Minimization"*, which provides a comparison of the performance of
various heuristics and exact algorithms for crossing minimization.

For counting the number of edge crossings between two layers we use the `O(|E|
log |V_small|)` algorithm described in *Barth, et al., "Simple and Efficient
Bilayer Cross Counting"*.

For positioning (or coordinate assignment), we derived our algorithm from
*Brandes and Köpf, "Fast and Simple Horizontal Coordinate Assignment"*. We made
some some adjustments to get tighter graphs when node and edges sizes vary
greatly.

## Third Party Examples

Dagre has been included as a part of some very cool projects. Here are just a
couple that stand out:

[JointJS][] has a plugin that uses dagre for layout. JointJS focuses on
rendering and interaction with diagrams, which synergizes well with Dagre. If
you want the ability to move nodes and manipulate edges interactively, this is
a good place to start!

Jonathan Mace has a
[demo](http://cs.brown.edu/people/jcmace/d3/graph.html?id=small.json) that
makes it possible to interactively explore graphs. In his demo, you can
highlight paths, collapse subgraphs, via detailed node information, and more!

## License

dagre is licensed under the terms of the MIT License. See the LICENSE file
for details.

[npm package manager]: http://npmjs.org/
[D3]: https://github.com/mbostock/d3
[JointJS]: http://www.daviddurman.com/automatic-graph-layout-with-jointjs-and-dagre.html
