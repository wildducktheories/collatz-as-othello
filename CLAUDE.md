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

### 3. Conservation Laws - Correct Semantics

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
- Transformation: 1 pebble at (j,i) → 1 same color at (j+1,i+2) + 1 opposite color at (j+1,i)
- Based on identity: g = h² - 1

**5x+1 System (g = h² + h - 1):**
- Example: g=5, h=2 (since 5 = 2²+2-1) ← **Default example is 5x+1!**
- Transformation: 1 pebble at (j,i) → 1 same at (j+1,i+2) + 1 same at (j+1,i+1) + 1 opposite at (j+1,i)
- Based on identity: g = h² + h - 1

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

## Future Enhancements

- Validation of initial state constraints
- Animation of pebble movements
- Step-by-step tutorial/walkthrough
- More example configurations
- Mobile touch gesture support
- Reverse operations for special laws
- Export board as image

## References

Based on research paper: "Othello Board Analogy for Collatz-Type Cycles" by Jon Seymour
