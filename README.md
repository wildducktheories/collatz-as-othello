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
- **X-Cycle Navigation**: Browse through related cycle elements
- **P-Cycle Display**: Shows all p-values in the current cycle
- **Conservation Assertion**: Validates polynomial sum preservation (Σ c_{j,i}·g^(o-1-j)·h^i) after every operation

### Persistence & Debugging

- **URL Sharing**: Encode complete board state in URL parameters
- **Local Storage**: Save and load named configurations
- **JSON Export/Import**: Download/upload board configurations with full undo/redo history
- **History Replay**: Export includes complete session history for debugging
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
- Base examples: p=9, 17, 33, 65
- Additional: p=37, 73, 281, 293, 585, 2119, 4681, 8301
- Relationship: g = h² - 1 (3 = 4 - 1)

**5x+1 System (g=5, h=2)**
- Base examples: p=9, 17, 33, 65
- Additional: p=133, 1045, 1093
- Relationship: g = h² + h - 1 (5 = 4 + 2 - 1)

**7x+1 System (g=7, h=2)**
- Base examples: p=9, 17, 33, 65
- Relationship: g = h² + 2h - 1 (7 = 4 + 4 - 1)

**Other Systems**
- g=8, h=3: p=585
- g=181, h=2: p=131089, 131201

### Spread Mode

Enable "Spread" mode to automatically repeat Left/Up operations until all multiples are reduced to singletons - useful for quickly simplifying board states.

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
