---
title: "CSP, SAT && RL"
linkTitle: "CSP, SAT && RL"
weight: 4
description: >
  How Luet turns Image resolution into CSP
---

Under the hood, Luet uses boolean satisfiability problem ([SAT](https://en.wikipedia.org/wiki/Boolean_satisfiability_problem))  [reinforcement learning](https://en.wikipedia.org/wiki/Reinforcement_learning) techniques to solve package constraints.

Luet allows you to specify 3 types of set of contraints on a [package](/docs/docs/concepts/packages/) definition:

- Requires
- Conflicts
- Provides

The package definition in your tree definition, along with its Requires and Conflicts, are turned into Boolean formulas that are consumed by the solver to compute a solution. The solution represent the state of your system after a particular query is asked to the solver (Install, Uninstall, Upgrade).

## Requires and Conflicts

A list of requires and conflicts, composed of one or more [packages](/docs/docs/concepts/packages/), becomes a SAT formula. The formula is then given to the SAT solver to compute a finite state set of packages which must be installed in the system in order to met the requirements.

Alongside, Luet builds the ALO (At least one) and AMO (At most one). <!-- I think this sentence should be rewritten-->This means that given requirements as selectors in semver notation (e.g. `>1.0`) it builds additional formulas to restrict the constraints of the given solution.

At the moment of writing Luet is not encoding any order, but relies on the SAT solver and tries to enforce the order by forcing the engine to look at the "best" pick among a set of package. This is a known issue that we will address in the near future.

## Provides

Provides constraints are not encoded in a SAT formula. Instead, they are `expanded` into an in-place substitution of the packages that they have to be replaced with.
They share the same SAT logic of expansion, allowing to swap entire version ranges (e.g. `>=1.0`), allowing to handle package rename, removals, and virtuals.

## References

- OPIUM (Luet is inspired by it): https://ranjitjhala.github.io/static/opium.pdf
- FROM TRACTABLE CSP TO TRACTABLE SAT: https://www.cs.ox.ac.uk/files/4014/maxclosed_orderencoding_v16_TR.pdf
- Solver concepts applied to packages (`zypper`): https://en.opensuse.org/openSUSE:Libzypp_satsolver_basics
