# Changelog

## Unreleased

### x vs Δk Visualisation

Added an interactive **x vs Δk plot** that displays cycle elements as integer lattice
points on the affine line

```
x = k̂/γ + (1/γ)·Δk
```

where `k̂` is the minimum k-value in the cycle, `γ = gcd(k, d)`, and `Δk = k − k̂`.

**Cycle element dots**

- Red dots — odd p-value elements (the gx+q step is applied)
- Green dots — even p-value elements (the x/h step is applied)
- The current element is drawn larger with a white ring

**Reference series (informational, one dot per lattice point)**

- Small green dots — gx+q values across all lattice points in the visible range
- Small red dots — h·x values across all lattice points in the visible range

**Guidelines on hover**

When a cycle element is selected, dashed guidelines show where it is headed next:

- Odd-p element (red): vertical line up to gx+q, then horizontal to the destination
  lattice point — endpoint rendered green (the destination is a green element)
- Even-p element (green): horizontal line to Δk(x/h), then vertical down to x/h —
  endpoint rendered red (the destination is a red element)

**Δk_max reference line**

A labelled dashed line marks the upper bound on Δk for odd p-value elements.  Even
p-value elements in forced cycles (γ > 1) may fall outside this bound.

**Controls**

- **Evens checkbox** — show or hide even p-value elements
- **Cycle button** — animates through the cycle, auto-tracking the current element
- **Popout button** — opens the plot in a resizable popup window with its own Cycle button
- **#delta-k anchor** — deep-link directly to the x vs Δk section

### Papers

- Added `papers/othello.tex` — "Othello Board Analogy for Collatz-Type Cycles"
- Added `papers/x-vs-delta-k.tex` — analysis of cycle element positions on the affine
  lattice line, including the Δk_max bound for odd p-value elements
- Added `Taskfile.yml` with `pdf`, `pdf:othello`, `pdf:x-vs-delta-k`, `clean`, and
  `clean:all` tasks for building papers with pdflatex

### Repository

- Added `.gitignore` rules for pdflatex build artefacts, `.task/`, and `.DS_Store`
