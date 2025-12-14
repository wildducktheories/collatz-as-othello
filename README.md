# Collatz Cycles as Othello: Interactive Board Explorer

An interactive web application for exploring Collatz-type cycles using the Othello board analogy. This tool provides a visual and mathematical interface for manipulating polynomial representations of cycle elements.

🎮 **[Launch the Application](https://wildducktheories.github.io/collatz-as-othello/)** (Latest Version)

📦 **[Source Repository](https://github.com/wildducktheories/collatz-as-othello/)**

## Overview

This project implements a novel approach to understanding Collatz-type cycles by representing them as configurations of pebbles on a grid (the "Othello board"). Each position on the board corresponds to a term in a polynomial of the form:

**p(g,h) = q·k(g,h) - x₀·d(g,h) = 0**

Where:
- `p` encodes the cycle structure in its binary representation
- `g, h` are the system parameters (e.g., g=5, h=2 for the 5x+1 system)
- `k(g,h)` is the polynomial derived from p's binary structure
- `d(g,h) = h^e - g^o` is the difference polynomial
- `q, x₀` are the derived cycle parameters

## Features

### Core Functionality

- **Interactive Grid**: Click cells to select them and apply conservation laws
- **Conservation Laws**: Six transformation operations that preserve the polynomial value:
  - **Left/Right**: Multiply by g (combine/split pebbles horizontally)
  - **Up/Down**: Multiply by h (combine/split pebbles vertically)
  - **Basis Law**: Universal transformation using g = (g+1) in base h, minus 1 (works for any g, h)
  - **Reverse Basis Law**: Inverse of the basis law transformation

### Visual Representation

- **Pebbles**: White for positive coefficients, black for negative
- **Coordinate System**: i=0 at bottom (mathematical convention), j increases left to right
- **Grid Position (j, i)**: Represents the coefficient of g^(o-1-j)·h^i

### Keyboard Controls

Fast, prompt-free operation with keyboard shortcuts:

- **l/r/u/d/f/b**: Apply operations with 1 pebble
- **Shift + key**: Apply operations with maximum available pebbles
- **q**: Leave q pebbles (cycle parameter), move rest right - useful for reconstructing q·k(g,h)
- **v**: Reset q·k (clear board to x₀·d part) - allows reconstruction of the q·k(g,h) part of the identity
- **k**: Distribute (q·k) - inverse of Reset q·k, spreads consolidated pile back to monomials
- **m**: Start/stop Game of Death mode (entropy reduction until death/empty board)
- **Ctrl/Cmd + Z**: Undo
- **Ctrl/Cmd + Shift + Z** or **Ctrl/Cmd + Y**: Redo

### Input Formats

The application supports three ways to specify p-values:

1. **Decimal**: `1093`
2. **Binary**: `0b10001000101`
3. **OE Notation**: `OEEOEEEO` (O=odd bit, E=even bit, LSB first)

All formats generate clickable links for easy sharing.

### Mathematical Features

- **Polynomial Display**: LaTeX-rendered equations showing k(g,h) and d(g,h)
- **Force Balance Equation**: Shows white terms (positive coefficients) on LHS equal to black terms (negative coefficients) on RHS, demonstrating the equilibrium of opposing forces
- **X-Cycle Navigation**: Browse through related cycle elements
- **P-Cycle Display**: Shows all p-values in the current cycle
- **Conservation Assertion**: Validates polynomial sum preservation (Σ c_{j,i}·g^(o-1-j)·h^i) after every operation

### Analysis & Visualization

- **Action Statistics Graph**: Real-time timeseries visualization showing cumulative count of each conservation law operation:
  - Color-coded lines for each action type (gRight, gLeft, hUp, hDown, basisLaw, reverseBasisLaw)
  - Total action count overlay
  - Interactive legend with current counts
  - Updates during undo/redo and animation
  - Helps identify which operations are used most frequently

- **Entropy & Force Analysis Graph**: Dual-metric timeseries showing board evolution:
  - **Board Entropy** (purple line): Measures complexity using formula `log₂(|c_{j,i}| + 1) + (1 if occupied else 0) + i + (o-1-j)·log₂(3)` summed over all occupied cells
  - **Balanced Force** (green line): Sum of forces experienced by white pebbles `Σ c_{j,i}·g^(o-1-j)·h^i` (for c_{j,i} > 0), indicating magnitude of balanced forces
  - Dual y-axes with independent scaling
  - Shows how board complexity and force balance change during exploration
  - Scientific notation for large force values

### Persistence & Debugging

- **URL Sharing**: Encode complete board state in URL parameters
- **Local Storage**: Save and load named configurations
- **JSON Export/Import**: Download/upload board configurations with full undo/redo history
- **History Replay**: Export includes complete session history for debugging (action-based storage supports up to 10,000 actions)
- **Animation**: Step-by-step playback of history at 500ms intervals
- **Anchor Parameter**: Maintain stable p-cycle ordering when navigating

## Getting Started

### Quick Start

1. Visit the [live application](https://wildducktheories.github.io/collatz-as-othello/)
2. Try the default example (p=1093, g=5, h=2) or click "Load Example (17 Cycle)"
3. Click a cell with pebbles to select it
4. Use keyboard shortcuts or buttons to apply conservation laws
5. Watch the polynomial transform while preserving its total value

### Example Systems

**3x+1 System (g=3, h=2)**
- Examples: p=9, 17, 33, 37, 65, 73, 281, 293, 585, 2119, 4681, 8301
- Relationship: g = h² - 1 (3 = 4 - 1)

**5x+1 System (g=5, h=2)**
- Examples: p=9, 17, 33, 65, 133, 1045, 1093
- Relationship: g = h² + h - 1 (5 = 4 + 2 - 1)

**7x+1 System (g=7, h=2)**
- Examples: p=9, 17, 33, 65
- Relationship: g = h² + 2h - 1 (7 = 4 + 4 - 1)

**Other Systems**
- g=8, h=3: p=585
- g=181, h=2: p=131089, 131201

### Spread Mode

Enable "Spread" mode to automatically repeat Left/Up operations until all multiples are reduced to singletons - useful for quickly simplifying board states.

## Distribution Controls

### Reset q·k and Reconstruction Workflow

The **Reset q·k (v)** button provides a powerful workflow for understanding the polynomial identity:

**The Identity:** q·k(g,h) - x₀·d(g,h) = 0

Where:
- **q·k(g,h)**: The "white terms" polynomial (positive coefficients)
- **x₀·d(g,h)**: The "black terms" polynomial (negative coefficients)
- **d(g,h) = h^e - g^o**: The difference polynomial

**How Reset q·k Works:**

1. **Press 'v' or click "Reset q·k"** - clears the board to show only the x₀·d(g,h) part:
   - Shows x₀ white pebbles at position (-1, 0) representing x₀·g^o
   - Shows x₀ black pebbles at position (o-1, e) representing -x₀·h^e
   - This is the "-x₀·d(g,h)" part of the identity

2. **Use conservation laws to reconstruct q·k(g,h)** - build up the white polynomial part:
   - The **'q' shortcut** is particularly useful: leaves q pebbles, moves (c-q) right
   - Apply other operations (l/r/u/d/f/b) to build the polynomial structure
   - Goal: reach a state where all pebbles cancel (empty board = zero state)

3. **Reaching zero proves the cycle** - when you successfully cancel all pebbles:
   - You've demonstrated that q·k(g,h) = x₀·d(g,h)
   - This confirms the polynomial identity holds
   - This proves the existence of the cycle

**Example Workflow:**
- Load p=1093, g=5, h=2 (the 17 cycle with q=1)
- Press 'v' to reset → shows 17 white at (-1,0) and 17 black at (2,7)
- Use 'q' and other operations to build up the k(g,h) polynomial
- Successfully reaching zero state proves the identity

### Distribute (q·k) - The Greedy Algorithm

The **Distribute (q·k) (k)** button is the **inverse of Reset q·k** - it takes the consolidated pile created by Reset q·k and distributes it back to the individual monomial positions.

**What Makes It Special:**

This algorithm operates **without knowledge of the goal state** - it uses only:
- Local pebble counts at each position
- A record of which columns already have deposits
- A simple greedy rule: "try to move up; if you leave a remainder, that's a mistake"

**The Algorithm:**

Starting from the consolidated pile at (o-1, 0) (rightmost column, bottom row):

1. **Try to move up** using h-multiplication:
   - Calculate: maxUp = ⌊count / h⌋
   - If remainder = 0, move up and continue
   - If remainder > 0, this is a **"mistake"** → proceed to step 2

2. **The "mistake" reveals the need for a left excursion:**
   - Perform the up move anyway (demonstrating the mistake)
   - **Backtrack** by moving down (undoing the mistake)
   - Identify the leftmost column in this row without deposits
   - Execute a **left excursion** to that column

3. **Left excursion procedure:**
   - Go left from junction (o-1, i) to target column
   - Drop exactly **q pebbles** at the target
   - Return to junction, collecting pebbles along the way
   - Mark target column as having a deposit

4. **Repeat** until all pebbles are distributed

**The "Mistakes" Are Intentional:**

The up/down reversals you see (like operations 6-7 in the example) aren't bugs - they're **exploratory moves**:
- The algorithm tries to go up to row i+1
- Leaves a remainder behind → "we should have turned left back there"
- Backtracks and takes the left turn instead
- This demonstrates how a local greedy algorithm navigates without global knowledge

**Why This Matters:**

This algorithm shows that **you can reconstruct the monomial structure from just the consolidated sum** using only:
- Conservation laws (no "cheating" with direct placement)
- Local decisions (no global planning)
- A simple greedy heuristic

It's a constructive proof that the distribution exists and can be found algorithmically!

**Try It:**
1. Load any example (e.g., p=293, g=3, h=2)
2. Press 'v' to Reset q·k → creates consolidated pile
3. Press 'k' to Distribute → watch it systematically rebuild the monomials
4. Observe the "mistakes" (up/down reversals) as it explores

## Automation Controls

### Random Exploration

The **Random (r)** button provides automatic exploration of the board state:

- **Press 'r' or click the dice button** to start automatic random execution
- Randomly selects cells and actions across the entire board
- Executes conservation law operations at 500ms intervals
- Auto-stops when no actions are available
- **Press 'r' again** to stop at any time

**Fun Ways to Use Random:**

1. **Explore Example Cycles**: Load an example (like p=1093, g=5, h=2) and hit 'r' to watch it randomly navigate the board
2. **Fresh Start Exploration**: Use "Reset k (v)" to start from a clean slate, then let Random discover interesting sequences
3. **Lucky Discovery**: See if Random can stumble into the zero state by chance - proving the cycle!
4. **Pattern Recognition**: Watch Random explore and observe which operations it tends to use more frequently
5. **Checkpoint Navigation**: After using Reset k, watch how Random interacts with the checkpoint in the undo/redo history

Random mode makes exploration playful and can reveal unexpected transformation sequences you might not have tried manually.

### The Game of Death

**💀 A tip of the hat to John Conway's Game of Life**

While Conway's Game of Life creates complexity from simple rules, the **Game of Death** destroys complexity, systematically reducing entropy until the board reaches the empty state—**death**.

The **Game of Death (m)** button provides automatic entropy-reduction exploration:

- **Press 'm' or click the 💀 Game of Death button** to start the march toward death
- At each step, evaluates ALL possible actions and their effect on board entropy
- Selects from entropy-reducing actions using an inverse uniform distribution (see below)
- Executes conservation law operations at 500ms intervals
- **Expected outcome**: Given enough time, all boards eventually reach the empty state (death)
- **Press 'm' again** to stop at any time

**The Probabilistic Sampling Algorithm:**

The Game of Death uses a sophisticated sampling strategy with a **bias parameter**:

1. Sample u from uniform distribution U(0,1)
2. Calculate biased inverse: (1/u) × 2^bias
3. Round up to nearest power of 2: N = 2^⌈log₂(biased_inverse)⌉
4. Select randomly from top N entropy-reducing actions

**Bias Parameter Effects:**

- **bias=0** (default): Strong exploitation
  - P(N=1) = 50% — picks best action half the time
  - P(N=2) = 25%, P(N=4) = 12.5%, P(N=8) = 6.25%, etc.

- **bias=1**: Balanced exploration
  - P(N=2) = 50%, P(N=4) = 25%, P(N=8) = 12.5%, etc.
  - Shifts distribution toward larger candidate pools

- **bias=2+**: Strong exploration
  - Allows more high-entropy actions into candidate pool
  - Helps escape local minima but may slow convergence

**Why "Death" is Inevitable:**

Given infinite time and proper bias settings, the Game of Death will eventually reach the empty board because:
- Conservation laws preserve the zero polynomial identity
- Entropy-reducing moves are always available until the zero state
- The empty board represents complete cancellation: death

**The Paradox of 1-Step, Global Optimization:**

Empirical observation reveals a counterintuitive phenomenon: **low-bias configurations that favor globally optimal entropy-reducing steps actually inhibit rapid transition to the final state.**

**The Mechanism:**

Low bias (bias=0) strongly favors the single best entropy-reducing action for the *next* move. However, this greedy strategy creates a critical problem:

1. **Island Formation**: Pebbles segregate into isolated "islands" of the same color
   - All-white island: pebbles of positive coefficients clustered together
   - All-black island: pebbles of negative coefficients clustered together

2. **Interaction Starvation**: Same-color islands almost never interact with each other
   - White pebbles move among themselves
   - Black pebbles move among themselves
   - No mixing between white and black

3. **Cancellation Failure**: Balanced force reductions require opposite colors to interact
   - Force balance equation: white terms = black terms
   - Cancellation happens when white and black pebbles meet
   - Without interaction, no cancellation → no path to zero state

**The Paradox:**

- **Low bias (greedy)**: Minimizes entropy at each step → forms color islands → prevents interaction → blocks cancellation → slow convergence
- **High bias (exploratory)**: Increases entropy in short term → mixes colors → enables interaction → allows cancellation → fast convergence

**Why Higher Entropy Actions Help:**

Biasing toward higher-entropy actions (bias > 0) paradoxically accelerates convergence:
- Short-term cost: entropy temporarily increases (seems wasteful)
- Long-term benefit: opposite-colored pebbles brought into proximity
- Critical effect: enables balanced force reductions (white + black → zero)
- Result: faster transition to death despite "inefficient" moves

**The Lesson:**

Optimizing for 1-step global optimality ≠ optimizing for multi-step global optimality. The globally optimal *next* move may destroy the path to the globally optimal *final* state. This is why Random mode (maximum mixing) often outperforms low-bias Game of Death—it continuously creates opportunities for opposite colors to interact and cancel.

**Practical Implications:**
- Start with bias=0 to observe island formation
- Increase bias to 1-2 to watch mixing enable cancellations
- Compare convergence times: low bias vs high bias vs Random
- The Game of Death teaches that sometimes you must increase chaos to achieve order (death).

## How It Works

### Parameter Derivation

Given p, g, h, the application:

1. **Parses p's binary structure** to extract monomials (terms in k(g,h))
2. **Computes o, e** (the powers of g and h)
3. **Evaluates k(g,h)** = sum of monomial values
4. **Computes d(g,h)** = h^e - g^o
5. **Derives q and x₀** using gcd(k, d)

For detailed mathematics, see [CLAUDE.md](CLAUDE.md).

### Conservation Laws

All operations preserve the polynomial identity by applying valid algebraic transformations:

- **g-multiplication**: Uses g^(o-1-j) = g·g^(o-2-j)
- **h-multiplication**: Uses h^(i+1) = h·h^i
- **Basis laws**: Universal transformation using the identity **g = (g+1) in base h, minus 1**
  - Works for ANY g, h values by expressing g+1 in base-h representation
  - **Forward law**: Replaces a pebble at (j,i) with the base-h digits of (g+1) at column j+1, minus 1 at i=0
  - **Reverse law**: Uses h^k = (g+1) - (sum of lower power terms) to reverse the transformation
  - Examples:
    - g=3, h=2: g+1=4=100₂ → g = h² - 1 (3x+1 system)
    - g=5, h=2: g+1=6=110₂ → g = h² + h - 1 (5x+1 system)
    - g=7, h=2: g+1=8=1000₂ → g = h³ - 1 (7x+1 system)
    - g=11, h=2: g+1=12=1100₂ → g = h³ + h² - 1 (11x+1 system)
- **Assertion**: Every operation validates polynomial sum is unchanged

#### Force Conservation Analogy

The conservation laws can be understood through a physical analogy:

- **Electric Field**: The board can be considered to be suffused with an electric field whose strength at position (j,i) is g^(o-1-j)·h^i
- **Pebbles as Charges**: Each pebble carries a unit charge: white = +1, black = -1
- **Net Charge**: The net charge at square (j,i) is c_{j,i} (positive, negative, or zero)
- **Force**: The electric force at each square equals charge × field strength = c_{j,i}·g^(o-1-j)·h^i
- **Conservation Principle**: Across the entire board, the net force is always zero:

  **Σ c_{j,i} · g^(o-1-j) · h^i = 0**

All operations preserve this **force conservation** — they rearrange charges (pebbles) without changing the net force. This is why they're called "conservation laws" rather than just "transformations."

### The Othello Analogy

The board is called "Othello" because:
- Pebbles have two colors (white/black for positive/negative)
- Conservation laws can flip signs (like flipping pieces in Othello)
- The special Collatz relations create patterns of sign changes

## Technical Details

- **Implementation**: Single-page vanilla JavaScript application
- **No Dependencies**: Pure HTML5, CSS3, and ES6 JavaScript
- **Mathematical Rendering**: KaTeX for LaTeX display
- **Deployment**: Static hosting (S3, GitHub Pages, etc.)
- **File Size**: ~85KB single HTML file

## Use Cases

### Research

- Explore cycle structures in Collatz-type sequences
- Test conjectures about cycle properties
- Visualize polynomial transformations
- Generate and analyze cycle families

### Education

- Teach polynomial manipulation with visual feedback
- Demonstrate conservation laws in action
- Explore number theory through interactive experimentation
- Connect binary representations to algebraic structures

### Exploration

- Discover new cycles by manipulating board states
- Navigate between related cycle elements (x-cycles and p-cycles)
- Find patterns in polynomial structures
- Test system behavior with different g, h parameters

## Development

### Local Setup

```bash
# Clone the repository
git clone https://github.com/wildducktheories/collatz-as-othello.git
cd collatz-as-othello

# Open in browser (no build step required)
open index.html
```

### Architecture

**Core Classes:**
- `BoardState`: Grid representation with Map<"j,i", count>
- `ConservationLaws`: All transformation rules
- `Renderer`: Canvas-based visualization
- `Application`: Main controller
- `History`: Undo/redo with 100-state limit
- `Persistence`: URL/LocalStorage/JSON handlers

See [CLAUDE.md](CLAUDE.md) for detailed implementation notes.

## Contributing

Contributions welcome! Areas of interest:

- Enhanced animation of individual pebble movements during transformations
- Mobile/touch interface improvements
- Additional example configurations
- Performance optimizations for large boards
- Export board state as images
- Adjustable animation speed control

## References

Based on research exploring Collatz-type cycles through polynomial representations. The Othello board analogy provides an intuitive framework for understanding cycle structure and transformations.

**Research Paper**: "Othello Board Analogy for Collatz-Type Cycles" by Jon Seymour

## License

```
MIT License

Copyright (c) 2025 Jon Seymour

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

```

## Acknowledgments

Built with [Claude](https://claude.ai) using the [Claude Code](https://claude.com/claude-code) CLI tool.

---



**Questions or Comments?** See the [Reddit discussion thread](https://www.reddit.com/r/Collatz/comments/1pj1hjv/announcing_the_interactive_collatz_othello_board/) or the [research paper](https://wildducktheories.com/papers/the-othello-board.html).
