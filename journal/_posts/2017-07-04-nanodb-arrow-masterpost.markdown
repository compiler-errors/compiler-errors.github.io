---
layout: post
title: "Arrow: A NanoDB/Arrow Masterpost"
tags: [programming, scala, nanodb, arrow]
---

# NanoDB and Arrow

### Background

At Caltech, Students are required to take a "track" of classes, beginning with an introduction to a programming subject, an in-depth implementation course, then a free-reign project course where we're required to choose a focus we're interested and implement a project pertaining to it. I chose the __databases__ track (CS 121/122/123) where we learn SQL and how databases operate on the front-end, implement a simple database in Java, and then then are free to choose some database-implementation focused project, possibly building off of the database we implement in the second course of the track.

In the implementation course (CS 122), we were able to explore databases hands-on by implementing various parts of a toy database called NanoDB. An important concept in the design of the database is the planning pipeline, where SQL statements are parsed and turned into a "plan tree" with nodes resembling operators in relational algebra, and then the tree is executed to produce table results to the end-user. The database's code touched many different levels of operation, from high-level parsing and SQL representation to low-level filesystem and storage concerns. Along this pipeline, optimization details are sprinkled in to make sure that even naïve SQL queries are made efficient, since SQL is a declarative language and thus the programmer is free to change implementation details to produce the same results.

### Problem

Throughout CS 122, I ran into many frustrations with implementing some of the key features of this pipeline. Often a naïve implementation would be simple enough to write in Java, but as optimization details and more complicated specifications were considered, my team's code became messy, corner cases had to be continually reconsidered and patched, and it seemed like many stages of the pipeline began to bleed together which ruined the modularity of the database. After a while, it dawned on me that Java may not have been the best language to implement the whole database in.

Many of the optimizations and functionalities that we struggled so hard to support were hindered by the fact that Java lacks a lot of the expressive power of a functional languages. Some of the operations on the plan tree that we produce after SQL parsing are natural to express in functional languages, but require a lot of boiler-plate code and are sometimes impossible to generalize with simple, readable Java code. However, since the scope of NanoDB was so large, simply switching to a functional language would produce the same problem, since imperative code was great at expressing some of the lower-level storage code and would just create a similar problem if ported to a language like Haskell or OCaml.

### Solution

The track instructor, Donnie, sent me an article about a neat optimizer called Catalyst and how it used Scala to implement optimizations as a series of rewrite rules. Along with a classmate of mine, for our project, we chose to integrate a similar optimizer architecture on top of NanoDB's existing codebase. Our project, which I'll call __Arrow__, was inspired in part by the rewrite rule architecture of Catalyst, and since Scala is a JVM language, we decided that we could also use the language for our portion of the project course and keep the low-level details (which we weren't going to focus on) in Java, meaning that we had less non-essential code to worry about and a quick off-the-ground time to explore the more interesting parts of Scala and our own optimizer architecture.

While we started out aiming to implement a simple rewrite rule-based architecture, we ended up making some huge changes to the planning pipeline as well. By implementing some core idioms found in Scala, we found NanoDB to be much more extensible, cleaner, and still very well structured into distinct planning phases that we found that began to break down when implementing optimizations in our previous iterations using Java.

## Roadmap

Over the rest of the summer I'm planning on making some blog posts detailing Arrow and the changes to NanoDB that we implemented during our 10 week project course. I'll edit this list over the summer to link to actual articles, but I'd like to go into more detail about some of the design choices I made while implementing Arrow, and why they were chosen (whether they were due to Scala, due to our rules framework, or just a better long-term choice).

Some of the meaty posts I plan on writing are:
* Restructuring the Planning Pipeline
* Fixing the Schema objects (Natural Join conundrums)
* Implementing the (naïve) Logical Planner
* Implementing the Logical Optimizer
* Implementing the Physical Planner
* Optimizing and Evaluating Correlated Subqueries
* Implementing Decorrelations (finally!!)
* ANTLR4 and Scala's Impacts on NanoDB's Extensibility
* Future Goals

This list will probably change, but each one represents a significant area of focus during the project course and an area where many things were learned, and hopefully learning which can be folded back into the pre-Java implementation of NanoDB which we forked (that is, important findings that transcend just Scala or the rewrite rule-based optimizer we chose to implement).
