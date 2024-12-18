- https://github.com/SteveJustin1963/tec-MINT

# tec-Ternary

## Tri Logic

# SQL and Three-Valued Logic on 8-bit Systems

## Three-Valued Logic Foundation
SQL's fundamental operation relies on three-valued (ternary) logic, distinct from binary logic used in typical CPU operations. The three states are:
- TRUE (typically represented as 01)
- FALSE (typically represented as 00)
- UNKNOWN (typically represented as 10)

When implementing on 8-bit systems, each logical value requires 2 bits for proper representation. This means a single byte can store 4 logical values.

## NULL Value Implementation
NULL values in SQL represent unknown data, not just missing data. The implementation requires:
1. Two bits per logical value for proper three-valued logic representation
2. Special comparison operators that handle the UNKNOWN state
3. Truth tables that correctly propagate UNKNOWN through logical operations

For example, the truth table for AND operations would be:
```
AND      | TRUE    | FALSE   | UNKNOWN
---------|---------|---------|--------
TRUE     | TRUE    | FALSE   | UNKNOWN
FALSE    | FALSE   | FALSE   | FALSE
UNKNOWN  | UNKNOWN | FALSE   | UNKNOWN
```

## 8-bit Database Implementation
On an 8-bit CPU like the Z80, a basic database system can be implemented with several key components:

### Storage Structure
1. Fixed-length Record Files
- Header block containing schema information
- Data blocks with fixed-length records
- Each field requires an additional status bit for NULL tracking

2. Index Files
- B-tree structure limited to available memory
- Index nodes containing:
  - Key value
  - NULL indicator bits
  - Block pointer (16-bit address)

### Memory Management
Given the Z80's 64KB address space:
- Buffer pool for active data pages (typically 1-4KB)
- Index cache for frequently accessed nodes
- Working memory for query processing

### Query Processing
1. Sequential Operations
```z80
; Example Z80 pseudocode for NULL-aware comparison
CompareWithNull:
    ld a, (FieldStatus)    ; Load NULL status
    bit 7, a              ; Check NULL bit
    jr nz, HandleNull     ; Jump if NULL
    ; Normal comparison code
    ret
HandleNull:
    ld a, UNKNOWN_FLAG    ; Load UNKNOWN result
    ret
```

2. Index Operations
- Traverse B-tree with NULL-aware comparisons
- Handle UNKNOWN results in branch decisions

### Limitations
1. Memory Constraints
- Maximum database size limited by addressable storage
- Index depth restricted by available RAM
- Buffer pool size affects performance

2. Performance
- Three-valued logic comparisons require more CPU cycles
- Complex operations like JOINs require careful memory management
- Index maintenance overhead is significant

## Real-world Applications
This implementation would be suitable for:
- Embedded systems requiring structured data storage
- Small-scale data management applications
- Systems with limited memory resources

The system trades off functionality for reliability within hardware constraints. While not suitable for large-scale applications, it provides a solid foundation for understanding how SQL's three-valued logic can be implemented on constrained hardware.

### Implementation Considerations
When implementing this system, particular attention must be paid to:
- Memory allocation and deallocation strategies
- Buffer management to minimize disk I/O
- Proper propagation of NULL values through calculations
- Transaction boundary management within memory constraints

# Implementation on 8-bit systems, specifically focusing on the Z80 architecture. 

# Three-Valued Logic and Database Implementation on 8-bit Systems

## Three-Valued Logic Foundation

SQL uses three-valued (ternary) logic for handling NULL values, which is fundamentally different from the binary logic typically used in CPU operations. The three logical states are:

- TRUE (typically represented as 01)
- FALSE (typically represented as 00) 
- UNKNOWN (typically represented as 10)

### Truth Tables for Ternary Logic

#### AND Operation
```
AND      | TRUE    | FALSE   | UNKNOWN
---------|---------|---------|--------
TRUE     | TRUE    | FALSE   | UNKNOWN
FALSE    | FALSE   | FALSE   | FALSE  
UNKNOWN  | UNKNOWN | FALSE   | UNKNOWN
```

#### OR Operation
```
OR       | TRUE    | FALSE   | UNKNOWN
---------|---------|---------|--------
TRUE     | TRUE    | TRUE    | TRUE   
FALSE    | TRUE    | FALSE   | UNKNOWN
UNKNOWN  | TRUE    | UNKNOWN | UNKNOWN
```

#### NOT Operation
```
Value    | Result
---------|--------
TRUE     | FALSE
FALSE    | TRUE
UNKNOWN  | UNKNOWN
```

## Implementation on 8-bit Systems

### Memory Representation

On an 8-bit system, representing three-valued logic requires careful consideration:

1. Each logical value requires 2 bits for proper representation
2. A single byte can store 4 three-valued logical values
3. Bit patterns:
   - 01: TRUE
   - 00: FALSE 
   - 10: UNKNOWN
   - 11: Reserved (not used)

### Database Implementation on Z80

For a Z80-based database system supporting three-valued logic:

1. Storage Structure:
```assembly
; Header block format
DB      TYPE        ; 1 byte record type
DW      LENGTH      ; 2 bytes record length
DB      FLAGS       ; 1 byte flags (bit 7: NULL tracking)
```

2. Record Format:
```assembly
; Fixed-length record structure
DB      STATUS      ; NULL indicators (1 bit per field)
DS      DATALEN     ; Actual data fields
```

3. Index Structure:
```assembly
; B-tree node format
DW      LEFT_PTR    ; Left child pointer
DW      KEY_VAL     ; Key value
DB      NULL_FLAGS  ; NULL status bits
DW      RIGHT_PTR   ; Right child pointer
```

### Memory Management

Given the Z80's 64KB address space, efficient memory management is crucial:

1. Buffer Pool
   - Typically 1-4KB reserved for active data pages
   - Least Recently Used (LRU) page replacement
   - Dirty page tracking for write optimization

2. Index Cache
   - Small cache (256-512 bytes) for frequently accessed nodes
   - Direct mapped for performance
   - Invalidation on updates

3. NULL Handling
```assembly
; Example Z80 assembly for NULL comparison
CheckNull:
    LD   A,(IX+0)     ; Load NULL status byte
    BIT  7,A          ; Check NULL bit
    JR   NZ,IsNull    ; Jump if NULL
    ; Normal comparison code
    RET
IsNull:
    LD   A,UNKNOWN    ; Load UNKNOWN result (10)
    RET
```

### Query Processing

1. Sequential Scan with NULL Handling:
```assembly
SeqScan:
    LD   IX,RECORD_PTR    ; Point to record
    LD   A,(IX+STATUS)    ; Load NULL status
    AND  FIELD_MASK       ; Check field NULL bit
    JR   NZ,HandleNull    ; Handle NULL case
    ; Process normal value
    RET
```

2. Index Operations:
```assembly
IndexLookup:
    LD   HL,ROOT_PTR      ; Load root node
    LD   A,(HL)          ; Load key
    CP   NULL_FLAG       ; Check for NULL
    JR   Z,NullPath      ; Special NULL handling
    ; Normal B-tree traversal
    RET
```

## Implementation Considerations

### Memory Constraints

1. Database Size Limitations:
   - Maximum database size: ~60KB (leaving room for code and buffers)
   - Maximum record size: 256 bytes
   - Maximum number of indexes: 8 (memory constraints)

2. Performance Optimizations:
   - Page-aligned buffers for faster access
   - Bitmap for NULL tracking
   - Minimal record header overhead

### Query Execution

1. Three-Valued Logic Evaluation:
   - Use lookup tables for complex operations
   - Cache common comparison results
   - Short-circuit evaluation where possible

2. Join Operations:
   - Nested loop joins with NULL awareness
   - Hash joins limited by memory
   - Sort-merge joins for large datasets

## Practical Limitations

1. Hardware Constraints:
   - Limited by Z80's 64KB address space
   - No hardware floating-point support
   - I/O bound by system bus speed

2. Software Constraints:
   - Limited transaction support
   - Basic concurrency control
   - Simple query optimization

## Best Practices

1. Database Design:
   - Use fixed-length records where possible
   - Minimize NULL usage
   - Keep indexes small and focused

2. Query Design:
   - Avoid complex joins
   - Use indexes effectively
   - Consider NULL handling overhead

3. Memory Management:
   - Regular buffer pool cleanup
   - Index cache maintenance
   - Monitor memory fragmentation



# MINT Code Examples for Three-Valued Logic and Database Operations

## 1. Three-Valued Logic Implementation

### Basic Three-Valued Logic Storage
```mint
// Store three-valued logic in two bits:
// 01 = TRUE
// 00 = FALSE  
// 10 = UNKNOWN
// Variable t for TRUE, f for FALSE, u for UNKNOWN

:T #01 t! ;     // Define TRUE
:F #00 f! ;     // Define FALSE  
:U #10 u! ;     // Define UNKNOWN
```

### Ternary AND Operation
```mint
:A              // Ternary AND function
  b! a!         // Store inputs in a and b
  a t = (       // If a is TRUE
    b .         // Return b
  ) /E (        // Else
    a f = (     // If a is FALSE 
      f .       // Return FALSE
    ) /E (      // Else (a must be UNKNOWN)
      b f = (   // If b is FALSE
        f .     // Return FALSE
      ) /E (    // Else
        u .     // Return UNKNOWN
      )
    )
  )
;
```

### Ternary OR Operation
```mint
:O              // Ternary OR function
  b! a!         // Store inputs
  a t = (       // If a is TRUE
    t .         // Return TRUE
  ) /E (        // Else
    a f = (     // If a is FALSE
      b .       // Return b
    ) /E (      // Else (a must be UNKNOWN)
      b t = (   // If b is TRUE
        t .     // Return TRUE
      ) /E (    // Else
        u .     // Return UNKNOWN
      )
    )
  )
;
```

### Ternary NOT Operation  
```mint
:N              // Ternary NOT function
  a!            // Store input
  a t = (       // If input is TRUE
    f .         // Return FALSE
  ) /E (        // Else
    a f = (     // If input is FALSE
      t .       // Return TRUE
    ) /E (      // Else
      u .       // Return UNKNOWN
    )
  )
;
```

## 2. Database Record Implementation

### Record Structure Definition
```mint
// Record structure with NULL tracking
:R                     // Create record structure
  [ 0                  // Status byte for NULL flags
    0 0               // Two data fields
  ] r!                // Store in variable r
;

// Set field value with NULL handling
:S                    // Set field value
  v! i! r!           // Get record, index, value
  v u = (            // If value is UNKNOWN
    r 0? #80 | r 0?! // Set NULL bit
  ) /E (             // Else
    r i 1 + v !      // Store value
  )
;
```

### Record Access with NULL Handling
```mint
:G                    // Get field value
  i! r!              // Get record and index
  r 0? #80 & (       // Check NULL bit
    u .              // Return UNKNOWN if NULL
  ) /E (             // Else
    r i 1 + ? .      // Return actual value
  )
;
```

## 3. Simple Database Operations

### Initialize Database
```mint
:I                    // Initialize database
  [ 0                // Status byte
    [ ]              // Empty record array
  ] d!               // Store in variable d
;
```

### Insert Record
```mint
:N                    // Insert new record
  r!                  // Get record
  d 1? /S s!         // Get size
  s 1 + d 1?!        // Increment size
  r d s 1 + !        // Store record
;
```

### Simple Query
```mint
:Q                    // Query with condition
  v!                  // Value to match
  d 1? /S (          // Loop through records
    d i 2 + ? x!     // Get record
    x 1? v = (       // If matches
      x .            // Output record
    )
  )
;
```

## 4. Index Implementation

### Create Index
```mint
:X                    // Create index
  [ 0                // Count
    [ ]              // Index entries
  ] x!
;

// Add index entry
:A                    // Add to index
  v! k!              // Key and value
  [ k v ]            // Create entry
  x 1? /S 1 +        // Get new position
  x 1? !             // Store entry
;
```

### Index Lookup
```mint
:L                    // Lookup in index
  k!                  // Key to find
  x 1? /S (          // Loop through entries
    x i 1 + ? y!     // Get entry
    y 0? k = (       // If key matches
      y 1? .         // Return value
    )
  )
;
```

## 5. Transaction Log

### Simple Transaction Log
```mint
:G                    // Begin transaction
  [ 0                // Transaction ID
    [ ]              // Operations list
  ] t!
;

// Log operation
:L                    // Log operation
  v! o!              // Operation and value
  [ o v ] n!         // Create log entry
  t 1? /S 1 +        // Get new position
  n t 1? !           // Store entry
;
```

## 6. Query Examples

### Simple Select Query
```mint
:S                    // Select records
  v!                  // Value to match
  `Records where value = ` v . /N
  d 1? /S (          // Loop through records
    d i 2 + ? r!     // Get record
    r 1? v = (       // If matches condition
      `Record ` i . `: ` r . /N  // Output
    )
  )
;
```

### Range Query
```mint
:R                    // Range query
  h! l!              // High and low values
  `Records in range ` l . ` to ` h . /N
  d 1? /S (          // Loop through records
    d i 2 + ? r!     // Get record
    r 1? l >=        // Check lower bound
    r 1? h <= & (    // And upper bound
      `Record ` i . `: ` r . /N  // Output
    )
  )
;
```

## 7. Memory Management

### Simple Memory Pool
```mint
:P                    // Initialize pool
  1000 /A p!         // Allocate 1000 bytes
  0 c!               // Clear counter
;

// Allocate block
:A                    // Allocate memory
  s!                  // Size needed
  c s + 1000 < (     // If enough space
    p c + n!         // Calculate address
    c s + c!         // Update counter
    n .              // Return address
  ) /E (             // Else
    `No space` /N    // Error message
  )
;
```

## Usage Examples

### Initialize and Use Database
```mint
I                     // Initialize database
[ 1 100 ] N          // Insert record
[ 2 200 ] N          // Insert another
100 Q                // Query for value 100
```

### Use Three-Valued Logic
```mint
T F A .              // TRUE AND FALSE
T U O .              // TRUE OR UNKNOWN
F N .                // NOT FALSE
```

### Transaction Example
```mint
G                     // Begin transaction
1 `INSERT` L         // Log insert
2 `UPDATE` L         // Log update
```

 # MINT Motor Control Implementation

## Constants and Variables
```mint
// Motor states stored in variables
// t = ON (TRUE), f = OFF (FALSE), u = UNKNOWN
#01 t!  // TRUE/ON  = 01
#00 f!  // FALSE/OFF = 00
#10 u!  // UNKNOWN = 10

// Motor states stored in a, b, c
// Output port for 74LS273 (based on schematic)
#04 p!  // Port for motor control
```

## Motor Control Functions

### Initialize Motors
```mint
:I              // Initialize function
  #00 p /O      // Clear all outputs
;
```

### Set Individual Motor
```mint
:M              // Set single motor (takes state and bit position)
  b! s!         // s = state, b = bit position
  s t = (       // If state is ON
    #01 b { p /O  // Shift 1 to position and output
  ) /E (
    #00 p /O    // Otherwise output 0
  )
;
```

### Control All Motors
```mint
:C              // Control all motors
  c! b! a!      // Get states for motors 1,2,3
  // Create output byte
  a t = ( #01 ) /E ( #00 ) // Motor 1
  b t = ( #02 | ) /E ( #00 | ) // Motor 2 
  c t = ( #04 | ) /E ( #00 | ) // Motor 3
  p /O          // Output to port
;
```

### Handle Unknown State
```mint
:U              // Unknown state handler
  `State Unknown` /N  // Print message
  #00 p /O      // Safety: all motors off
;
```

### Main Control Logic
```mint
:R              // Run motor control
  c! b! a!      // Get motor states
  // Check for unknown states
  a u = (
    U           // Handle unknown
  ) /E (
    b u = (
      U
    ) /E (
      c u = (
        U
      ) /E (
        C       // All states known, control motors
      )
    )
  )
;
```

## Example Usage
```mint
// Initialize
I

// Example: Motor 1 ON, Motor 2 OFF, Motor 3 UNKNOWN
t f u R

// Example: All motors ON
t t t R

// Example: All motors OFF 
f f f R
```

## Notes:

1. The code uses port 04h for output, which should connect to the 74LS273 latch shown in the schematic.

2. Each motor uses a single bit:
   - Motor 1: Bit 0
   - Motor 2: Bit 1
   - Motor 3: Bit 2

3. The three-valued logic uses:
   - 01 for ON/TRUE
   - 00 for OFF/FALSE
   - 10 for UNKNOWN

4. When any motor state is UNKNOWN, the system defaults to all motors off as a safety measure.

5. The implementation assumes active-high motor control through the 74LS273.

## Hardware Considerations

1. The TEC-1 schematic shows a 74LS273 octal D flip-flop that can be used for output control.
2. Motor control signals should be connected to the appropriate output pins of the 74LS273.
3. Additional buffering or driver circuits (not shown in code) may be needed between the 74LS273 and actual motors.
4. The Z80's I/O port 04h must be properly decoded to select the 74LS273.

