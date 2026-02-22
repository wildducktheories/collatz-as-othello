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

**Reference lines**

- Red line — gx+q, derived from the affine identity: (g·intercept + q) + g·slope·Δk
  (coloured red to match the odd/red source elements that use this step)
- Green line — h·x, derived from the affine identity: h·intercept + h·slope·Δk
  (coloured green to match the even/green source elements that use this step)

**Guidelines on hover**

When a cycle element is hovered, the crosshair and ring adopt the element's parity
colour (red for odd p, green for even p) rather than a fixed yellow.  Dashed guidelines
show where the element is headed next:

- Odd-p element (red): vertical line up to the gx+q line, then horizontal to the
  destination lattice point on the blue cycle line.  The first guideline dot (on the
  gx+q line) is red (source colour); the second dot (on the blue cycle line) is coloured
  by the destination element's own parity.
- Even-p element (green): horizontal line to the h·x line at Δk(x/h), then vertical
  down to x/h on the blue cycle line.  The first dot (on the h·x line) is green (source
  colour); the second dot (on the blue cycle line) is coloured by the destination
  element's own parity.

The hover info panel now leads with **p = \<value\> (odd p / even p)** in the element's
parity colour, followed by n, Δk, x, and gx+q.

**Δk_max reference line**

A labelled dashed line marks the upper bound on Δk for odd p-value elements.  Even
p-value elements may fall outside this bound; forced cycle elements (where p mod 2 ≠ x mod 2) need not respect it.

**Controls**

- **Evens checkbox** — show or hide even p-value elements
- **Cycle button** — animates through the cycle, auto-tracking the current element
- **GIF button** — downloads an animated GIF of one full cycle; a delay input (ms)
  next to the button controls the per-frame delay (default 1333 ms, ~0.75 fps)
- **Popout button** — opens the plot in a resizable popup window with its own Cycle button
- **#delta-k anchor** — deep-link directly to the x vs Δk section

**Keyboard shortcuts (canvas must be focused — hover over the plot)**

- `n` / `p` — navigate to the next / previous p-value in the cycle
- `←` / `→` — step hoveredN one lattice point left or right
- `Space` — snap to the nearest cycle element

Guidelines auto-track the current element whenever p changes (via n/p
keys, the Cycle animation, or an x-cycle link click).

**Bug fixes**

- Reference-dot loops (gx+q and h·x) are now skipped when individual
  lattice points are sub-pixel wide, preventing browser hangs on cycles
  with large Δk ranges (e.g. p=172705, g=3, h=2 has Δk_max = 13082)
- `XDeltaKGraph` is now created only once; previously it was re-created
  on every `app.initialize()` call, causing event-listener count to grow
  as 4·5^k after k keypresses and freezing the browser
- The x vs Δk plot is no longer disabled when d < 0; only d = 0
  (undefined slope) is treated as degenerate
- GIF frames previously showed only axis-tick labels with no stroked or
  filled shapes; two separate bugs were present:
  1. **LZW code-size sync bug** — the encoder bumped `cs` when
     `next === (1 << cs)` (same threshold as the decoder), but the encoder
     is always one table entry ahead of the decoder due to the KwKwK
     invariant.  This caused the encoder to write codes with the new
     (wider) bit-width one step before the decoder was ready to read at
     that width, silently corrupting all frames with more than ~254 unique
     colour sequences.  Fix: change the encoder bump condition to
     `next > (1 << cs)` so it fires one step later and aligns with the
     decoder's bump.
  2. **GPU readback on macOS Metal** — `getImageData` does not flush the
     GPU pipeline on Metal-backed canvases, so only CPU-rendered content
     (text labels) was captured.  Fix: after rendering, await
     `requestAnimationFrame` (GPU commands complete), call
     `canvas.toDataURL()` to read back the PNG, decode it CPU-side with
     `img.decode()`, draw onto a `willReadFrequently` canvas, and call
     `getImageData` — the CPU-decoded `<img>` source means no GPU flush
     is required on the readback canvas

### Papers

- Added `papers/othello.tex` — "Othello Board Analogy for Collatz-Type Cycles"
- Added `papers/x-vs-delta-k.tex` — analysis of cycle element positions on the affine
  lattice line, including the Δk_max bound for odd p-value elements
- Added `Taskfile.yml` with `pdf`, `pdf:othello`, `pdf:x-vs-delta-k`, `clean`, and
  `clean:all` tasks for building papers with pdflatex

### Repository

- Added `.gitignore` rules for pdflatex build artefacts, `.task/`, and `.DS_Store`
