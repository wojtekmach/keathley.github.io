---
layout: "post"
title: "property testing with elm"
date: "2016-10-03 22:13"
---

I've been working my way through Okasaki's "Purely Functional Data Structures"
in Elm. As part of that exercise I wanted to run property tests for each data
structure to make sure I wasn't violating any of the data structures invariants.
Getting property tests set up with Elm wasn't terribly difficult, but it was
tricky enough that I thought I would walk through the each of the steps.
