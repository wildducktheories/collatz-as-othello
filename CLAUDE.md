# Othello Board Collatz Cycle Explorer - Development Notes

## Project Overview

Interactive web application for exploring Collatz-type cycles using the Othello board analogy. Single-page vanilla JavaScript app deployed to S3.

## Key Implementation Lessons

### 1. Coordinate System

**Grid Layout:**
- Columns: j ranges from -1 to o-1 (left to right)
- Rows: i ranges from 0 to e (bottom to top)
- **CRITICAL**: i=0 at BOTTOM, i=e at TOP (mathematical convention, not screen coordinates)

**Monomial Mapping:**
- Position (j, i) represents coefficient of g^(o-1-j)·h^i
- Example with o=3: j=-1 → g^3, j=0 → g^2, j=1 → g^1, j=2 → g^0

### 2. Example Polynomial (x₀=17, g=5, h=2, q=1, o=3, e=7)

**Correct polynomial:** 17g³ + g² + gh + h⁴ - 17h⁷

**Grid positions:**
- (-1, 0): 17 white pebbles → 17g³
- (0, 0): 1 white pebble → g²
- **(1, 1)**: 1 white pebble → gh ← **NOT (0,1)!**
- (2, 4): 1 white pebble → h⁴
- (2, 7): 17 black pebbles → -17h⁷

**Bug fixed:** Initially placed gh term at (0,1) instead of (1,1)

### 3. Parameter Derivation from p, g, h

The application derives all parameters (o, e, k, d, q, x₀) from the input values p, g, h using the binary structure of p.

#### Step 1: Parse Binary Structure of p

Given p in decimal, convert to binary and parse from LSB to MSB (right to left):

**Algorithm (parseBinaryToStructure):**
```javascript
binary = p.toString(2)  // e.g., 1093 → "10001000101"
bits = reverse(binary)  // Read right to left: "10100010001"

monomials = []
hExponent = 0
i = 0

// Count initial 0s (E bits) for even p values
while i < length-1 and bits[i] == '0':
  hExponent++
  i++

// Parse monomials, excluding stop bit (MSB)
while i < length-1:
  if bits[i] == '1':
    monomials.append({hPower: hExponent})
    i++
    // Count consecutive 0s (E bits)
    evenCount = 0
    while i < length-1 and bits[i] == '0':
      evenCount++
      i++
    hExponent += evenCount
  else:
    i++

// Assign g powers based on position
o = monomials.length
for j in 0..o-1:
  monomials[j].gPower = o - 1 - j

// Find e (maximum h exponent)
e = max(m.hPower for m in monomials)
```

**Example: p=1093, g=5, h=2**
- Binary: 0b10001000101
- Bits reversed: 10100010001
- Parsing (excluding MSB):
  - Bit 0 = 1 → monomial at h⁰
  - Bits 1-3 = 000 → advance h exponent by 3
  - Bit 4 = 1 → monomial at h³
  - Bits 5-8 = 0001 → advance by 3, then monomial at h⁷
- Result: monomials = [{hPower:0, gPower:2}, {hPower:3, gPower:1}, {hPower:7, gPower:0}]
- o = 3, e = 7

#### Step 2: Compute k(g,h) - Polynomial Cofactor of q

Sum of all monomials evaluated at g, h:

```javascript
k(g,h) = Σ g^(monomials[i].gPower) × h^(monomials[i].hPower)
```

**Example: p=1093, g=5, h=2**
```
k(g,h) = g² × h⁰ + g¹ × h³ + g⁰ × h⁷
       = 5² × 1 + 5 × 8 + 1 × 128
       = 25 + 40 + 128
       = 193
```

#### Step 3: Compute d(g,h) - Polynomial Cofactor of x₀

The difference polynomial representing the cycle constraint:

```javascript
d(g,h) = h^e - g^o
```

**Example: p=1093, g=5, h=2**
```
d(g,h) = h⁷ - g³
       = 2⁷ - 5³
       = 128 - 125
       = 3
```

#### Step 4: Compute q and x₀

Using the cycle element identity: **p(g,h) = q·k(g,h) - x₀·d(g,h) = 0**

First compute gcd(k(g,h), d(g,h)), then:

```javascript
gcd_value = gcd(k(g,h), d(g,h))
x₀ = k(g,h) / gcd_value
q = d(g,h) / gcd_value
```

**Example: p=1093, g=5, h=2**
```
gcd(193, 3) = 1
x₀ = 193 / 1 = 193
q = 3 / 1 = 3
```

**Verification:**
```
p(g,h) = q·k(g,h) - x₀·d(g,h)
       = 3 × 193 - 193 × 3
       = 579 - 579
       = 0 ✓
```

#### Input Format Support

The `parseP()` function supports three input formats:

1. **Decimal:** `1093`
2. **Binary:** `0b10001000101` (standard 0b prefix)
3. **OE notation:** `OEEOEEEO` (O=odd bit/1, E=even bit/0, LSB first, no stop bit)

**OE notation parsing:**
- Left-to-right represents LSB to MSB
- 'O' → bit 1, 'E' → bit 0
- Automatically adds stop bit (1) at the MSB
- Example: "OEEOEEEO" → bits "10100010" + "1" → 0b110100010 = 418

### 4. Conservation Laws - Correct Semantics

#### Row Multiplication by h (Vertical Movement)

**Based on identity:** h^(i+1) = h·h^i

**Up (↑):**
- Source: h pebbles at (j, i)
- Result: 1 pebble at (j, i+1)
- Semantics: Combine h pebbles upward
- Example: 16 pebbles at i=0 → 8 pebbles at i=1 (with h=2)

**Down (↓):**
- Source: 1 pebble at (j, i)
- Result: h pebbles at (j, i-1)
- Semantics: Split 1 pebble downward

**Common mistake:** Initially had the directions reversed!

#### Column Multiplication by g (Horizontal Movement)

**Based on identity:** g^(o-1-j) = g·g^(o-2-j)

**Right (→):**
- Source: 1 pebble at (j, i)
- Result: g pebbles at (j+1, i)
- Semantics: Split pebble rightward

**Left (←):**
- Source: g pebbles at (j+1, i)
- Result: 1 pebble at (j, i)
- Semantics: Combine pebbles leftward

### 4. Special Collatz Relations

Detected automatically when g and h satisfy special relationships:

**3x+1 System (g = h² - 1):**
- Example: g=3, h=2 (since 3 = 2²-1)
- Forward Transformation: 1 pebble at (j,i) → 1 same color at (j+1,i+2) + 1 opposite color at (j+1,i)
- Based on identity: g = h² - 1
- Reverse Transformation (h² = g + 1): Uses h^i = h^(i-2) · (g + 1) = g·h^(i-2) + h^(i-2)
  - 1 pebble at (j,i) → 1 at (j-1,i-2) + 1 at (j,i-2)

**5x+1 System (g = h² + h - 1):**
- Example: g=5, h=2 (since 5 = 2²+2-1) ← **Default example is 5x+1!**
- Forward Transformation: 1 pebble at (j,i) → 1 same at (j+1,i+2) + 1 same at (j+1,i+1) + 1 opposite at (j+1,i)
- Based on identity: g = h² + h - 1
- Reverse Transformation (h² = g - h + 1): Uses h^i = h^(i-2) · (g - h + 1) = g·h^(i-2) - h^(i-1) + h^(i-2)
  - 1 pebble at (j,i) → 1 at (j-1,i-2) - 1 at (j,i-1) + 1 at (j,i-2)

### 5. Canvas Rendering

**Y-axis flipped for mathematical convention:**
```javascript
// Convert grid (j,i) to canvas pixels
gridToPixel(j, i) {
  const x = padding + (j - minCol) * cellSize;
  const y = padding + (maxRow - i) * cellSize;  // Note: maxRow - i (not i - minRow)
  return { x, y };
}
```

**Pebble visualization:**
- White pebbles: positive coefficients
- Black pebbles: negative coefficients
- Individual pebbles for count ≤ 10
- Large circle with number for count > 10

### 6. Persistence Features

Three methods implemented (all working):
1. **URL parameters**: btoa(JSON) encoded in query string
2. **LocalStorage**: Named save slots with timestamps
3. **JSON export/import**: Download/upload configuration files

### 7. Common Pitfalls Avoided

❌ **Wrong:** i=0 at top (screen coordinates)
✓ **Right:** i=0 at bottom (mathematical coordinates)

❌ **Wrong:** "Up" = split to i-1, "Down" = combine from i+1
✓ **Right:** "Up" = combine h→1 at i+1, "Down" = split 1→h at i-1

❌ **Wrong:** gh term at (0, 1)
✓ **Right:** gh term at (1, 1) for g^1·h^1

❌ **Wrong:** Separate g and h conservation laws
✓ **Right:** Special Collatz relations combine both

❌ **Wrong:** Reverse basis law placing terms at (j-1,i), (j,i-1), (j,i-2)
✓ **Right:** All terms go to i-2 and i-1 rows: (j-1,i-2), (j,i-1), (j,i-2) for 5x+1 or (j-1,i-2), (j,i-2) for 3x+1

### 8. Testing the App

**Load default example:**
- Click "Load Example (17 Cycle)"
- Should show 5 pebble stacks on grid
- Polynomial: 17g³ + g² + gh + h⁴ - 17h⁷
- System detected as: 5x+1 (g=5, h=2)

**Test "Up" operation:**
1. Select cell at (j=-1, i=0) with 17 pebbles
2. Click "↑ ×h Up"
3. Can move up to 8 sets (16 pebbles)
4. Result: 8 pebbles at (j=-1, i=1), 1 remains at (j=-1, i=0)

### 9. Architecture

**Single HTML file:** All CSS and JavaScript embedded for S3 deployment

**Core classes:**
- `BoardState`: Grid representation with Map<"j,i", count>
- `Renderer`: Canvas-based visualization with interaction
- `ConservationLaws`: All transformation rules
- `History`: Undo/redo with 100-state limit
- `Application`: Main controller
- `Persistence`: URL/LocalStorage/JSON handlers

**No dependencies:** Vanilla JavaScript, no build step required

## Deployment

Simply upload `index.html` to S3 bucket with:
- Public read access enabled
- Static website hosting configured
- Set as index document

## Implemented Features

### Polynomial Sum Conservation Assertion
- Every operation validates that Σ c_{j,i} · g^(o-1-j) · h^i is preserved
- Tolerance of 1e-9 for floating point comparisons
- Operations blocked with alert if conservation law violated
- Catches bugs in transformation implementations immediately

### Keyboard Controls
- **l, r, u, d, f, b keys** for quick operations without prompts
- Default: operates on 1 pebble
- **Shift + key**: operates on maximum available pebbles
- Keys ignored when typing in input fields
- Selected cell automatically moves to destination after operation

### UI Improvements
- Conservation law buttons organized in 3 rows (Left/Right, Up/Down, Basis/Reverse)
- Keyboard shortcuts shown in button labels (lowercase letters)
- Shift behavior documented in help text
- "Spread" mode for automatic repetition of Left/Up operations

### Multiple Identifier Formats
- p-value (decimal)
- b-value (binary with 0b prefix)
- oe-value (Odd/Even notation)
- All formats clickable to generate shareable URLs
- X-cycle display with current value shown as plain text (not clickable)
- Anchor parameter maintains stable p-cycle ordering

### LaTeX Polynomial Display
- KaTeX rendering for mathematical equations
- Shows p(g,h) = q·k(g,h) - x₀·d(g,h) = 0
- Separate sections for k(g,h) and d(g,h) polynomials
- Left-justified, italicized formatting

## Future Enhancements

- Validation of initial state constraints
- Animation of pebble movements
- Step-by-step tutorial/walkthrough
- More example configurations
- Mobile touch gesture support
- Export board as image

## References

Based on research paper: "Othello Board Analogy for Collatz-Type Cycles" by Jon Seymour
