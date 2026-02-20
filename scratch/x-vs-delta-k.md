**New paper + interactive visualisation: x vs Δk for Collatz-type cycles**

I've added a new short paper and a matching interactive plot to the
[collatz-as-othello](https://github.com/wildducktheories/collatz-as-othello) project.

---

**The paper** ([x vs Δk: Cycle Elements on the Affine Lattice Line](https://github.com/wildducktheories/collatz-as-othello/tree/master/papers/x-vs-delta-k.pdf))
shows that for any fixed cycle type (g, h, o, e), the cycle identity x·d = q·k can be
rewritten as a simple affine equation

> x = q·k̂/d + (q/d)·Δk

where k̂ is the minimum possible monomial sum for o odd steps and Δk = k − k̂ ≥ 0.
Every cycle element of that type must therefore be an integer lattice point on a single
line, within the computable bounds 0 ≤ Δk ≤ Δk\_max where

> Δk\_max = (h^(e−o) − 1)(k̂ − g^(o−1))

The bound is tight and applies to odd p-value elements; even p-value elements in forced
cycles (γ > 1) can fall outside it.

---

**The interactive plot** is now built into the
[live explorer](file:///Users/jon/personal/othello-board/index.html?p=293&g=3&h=2&anchor=293#delta-k):

- Red dots mark odd p-value cycle elements (gx+q is applied), green dots mark even
  p-value elements (x/h is applied)
- Hover over any element to see dashed guidelines showing where it goes next: odd
  elements follow a vertical-then-horizontal path to their green destination; even
  elements follow horizontal-then-vertical to their red destination
- Small green dots show gx+q values across all lattice points; small red dots show h·x
  values — giving a feel for the two series alongside the affine line
- A labelled Δk\_max dashed line marks the upper bound for odd elements
- A Cycle button animates through the cycle, tracking the current element on the plot;
  a Popout button opens a resizable standalone window

The paper folder also now contains the original
[Othello Board Analogy](https://github.com/wildducktheories/collatz-as-othello/tree/master/papers/othello.pdf)
paper for reference.

Feedback welcome!
