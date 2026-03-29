# Control Flow in Java

## 1. Program Flow Overview

### 1.1 What Program Flow Means

Program flow describes the order in which statements are executed at runtime.  
It determines:

- which statements are executed
    
- how many times they are executed
    
- when execution stops or exits early
    

All Java programs are built from three fundamental flow patterns:

1. Sequential execution
    
2. Branching (selection)
    
3. Iteration (repetition)
    

Most real programs are combinations of these three.

---

### 1.2 Sequential Execution (Default Flow)

#### Definition

Sequential execution means statements run top-to-bottom, one after another, exactly once, unless control flow is explicitly changed.

#### Key properties

- This is the default behavior of Java code
    
- Statements execute in the order they appear
    
- Later statements may depend on values produced by earlier statements
    

#### Typical example

```java
int x = 5;
int y = x + 2;
System.out.println(y);
```

#### Important notes / subtle points

- Method calls still follow sequential flow at the call site:
    
    - the call expression is evaluated
        
    - control transfers into the method
        
    - execution returns to the next statement after the method finishes
        
- Sequential flow can be interrupted by:
    
    - return (exit method)
        
    - throw (exception propagation)
        
    - loop control statements (break, continue)
        
    - branching statements (if, switch)
        

---

### 1.3 Branching (Selection)

#### Definition

Branching selects one execution path among multiple possible paths based on a condition or a value.

Branching exists to handle different cases or different inputs.

#### Branching mechanisms in Java

- if / else if / else
    
- switch statement
    
- switch expression (Java 14+)
    

#### Core idea

- A condition is evaluated or a value is matched
    
- Only the selected branch executes
    
- All other branches are skipped
    

#### Important notes / subtle points

- Conditions should be side-effect free whenever possible
    
- if / else if / else guarantees that only one branch executes
    
- Multiple independent if statements may execute more than one branch
    

---

### 1.4 Iteration (Repetition)

#### Definition

Iteration repeatedly executes a block of code while a condition holds or while traversing a sequence of values.

Iteration is used for:

- processing collections or arrays
    
- accumulating results (sum, min, max)
    
- repeated input validation
    
- simulations and repeated state updates
    

#### Loop constructs in Java

- for loop: when the number of iterations is known or index-based traversal is needed
    
- while loop: when the number of iterations is unknown and depends on a condition
    
- do-while loop: when the loop body must execute at least once
    

#### Core loop components

Every loop must have:

- initialization (starting state)
    
- condition (when to continue)
    
- update (progress toward termination)
    

If any component is incorrect, common bugs appear:

- infinite loops
    
- off-by-one errors
    
- missed updates causing non-termination
    

#### Important notes / subtle points

- Loop termination is the most critical design aspect
    
- break exits the loop immediately
    
- continue skips the remainder of the current iteration
    

---

### 1.5 How These Patterns Combine

Real programs rarely use a single flow pattern.  
Typical combinations include:

- sequential execution with branching
    
- loops containing conditional logic
    
- input loops with validation and termination conditions
    

A common mental model:

- read input
    
- validate input (branching)
    
- repeat until a condition is met (iteration)
    
- output results
    

---

### 1.6 Common Confusions Worth Avoiding

- Sequential flow can be interrupted by method returns and exceptions
    
- if / else if / else selects exactly one path
    
- Multiple if statements may select multiple paths
    
- Most loop bugs are caused by incorrect termination conditions
    

---

### 1.7 Practical Selection Guide

- Use sequential execution for ordered steps
    
- Use branching when decisions depend on state or input
    
    - ranges or complex logic: if
        
    - discrete values: switch
        
- Use iteration when work repeats
    
    - known iteration count: for
        
    - unknown iteration count: while
        
    - must execute at least once: do-while
---
## 2. Conditional Logic (if / else)

### 2.1 Purpose of Conditional Logic

Conditional logic allows a program to make decisions at runtime based on current state or input. Without conditionals, programs would always execute the same steps and could not react to data.

The core idea:

- evaluate a condition
    
- based on the result (true or false), choose a code path
    

---

### 2.2 The `if` Statement

#### Definition

The `if` statement executes a block of code only if a boolean expression evaluates to `true`.

```java
if (condition) {
// executed when condition is true
}
```

#### Rules

- The condition must be a boolean expression
    
- Java does not allow numeric values as conditions
    
- The block executes at most once
    

---

### 2.3 `if - else` and `if - else if - else`

#### `if - else`

```java
if (condition) {
// true branch
} else {
// false branch
}
```

- Exactly one branch executes
    
- If the `if` condition is true, the `else` branch is skipped
    

#### `if - else if - else`

```java
if (cond1) {
// branch 1
} else if (cond2) {
// branch 2
} else {
// default branch
}
```

Rules:

- Conditions are evaluated top to bottom
    
- The first true condition wins
    
- At most one branch executes
    

---

### 2.4 Mutual Exclusivity vs Independent Conditions

#### Mutually exclusive branching

Use `if - else if - else` when only one outcome should occur.

```java
if (score >= 90) {
grade = 'A';
} else if (score >= 80) {
grade = 'B';
}
```

#### Independent conditions

Use multiple `if` statements when multiple conditions may all be true.

```java
if (isLoggedIn) {
showProfile();
}
if (isAdmin) {
showAdminPanel();
}
```

---

### 2.5 Boolean Expressions and Short-Circuit Logic

#### Comparison operators

- `==`, `!=`
    
- `<`, `<=`, `>`, `>=`
    

#### Logical operators

- `&&` logical AND (short-circuit)
    
- `||` logical OR (short-circuit)
    
- `!` logical NOT
    

Short-circuit behavior:

- `A && B`: if A is false, B is not evaluated
    
- `A || B`: if A is true, B is not evaluated
    

---

### 2.6 Common Pitfalls

#### Assignment vs comparison

```java
// if (x = 5) { } // invalid in Java
if (x == 5) {

}
```

#### Side effects inside conditions

Avoid modifying variables inside conditions.

```java
// if (i++ > 5 && check()) { }
```
#### Missing braces

```java
if (condition)
doSomething();
doAnother(); // always executes
```

Best practice: always use braces.

---

### 2.7 `if` Is a Flow Tool, Not a Value Tool

The `if` statement controls execution flow, not value selection.

```java
int result;
if (x > 0) {
result = 1;
} else {
result = -1;
}
```

This pattern motivates the use of:

- ternary operator (`?:`)
    
- switch expressions
    

---

### 2.8 Mental Checklist

Before writing an `if` statement:

- Is the condition boolean?
    
- Should only one branch execute?
    
- Is the condition readable?
    
- Are braces used consistently?
    

---

### 2.9 Summary

- `if` executes code conditionally based on boolean expressions
    
- `if - else if - else` enforces mutual exclusivity
    
- Multiple `if` statements allow independent decisions
    
- Short-circuit logic affects evaluation order
    
- Most bugs come from unclear conditions, not syntax

---
## 3. Conditional Logic (switch)

### 1. Purpose of `switch`

The `switch` construct selects one execution path based on the value of a single expression.  
It is designed for **discrete, known-at-compile-time choices**, not for ranges or complex logic.

Use `switch` when:  
• the variable represents a fixed set of options  
• equality comparison is sufficient  
• readability is improved compared to long `if-else` chains

---

### 2. Traditional `switch` Statement

#### Basic syntax

```java
switch (value) {
case A:
    // logic A
    break;
case B:
    // logic B
    break;
default:
    // default logic
}
```

```java
switch (month.toLowerCase()) {  
    case "jan": case "feb": case "mar":  
        return "1st";  
    case "apr": case "may": case "jun":  
        return "2nd";  
    case "sep": case "jul": case "aug":  
        return "3rd";  
    case "oct": case "nov": case "dec":  
        return "4th";  
    default:  
        return null;  
}
```
#### Rules

• `value` must be char, byte, short, int, enum, String, or wrapper equivalents  
• comparison is done using `==` (or `.equals()` internally for String)  
• `break` prevents fall-through  
• `default` is optional but recommended

---

### 3. Fall-Through Behavior (Critical Concept)

Without `break`, execution continues into the next case.

```java
switch (x) {
case 1:
    System.out.println("one");
case 2:
    System.out.println("two");
}
```

If `x == 1`, both lines print.

Valid use case:  
• grouping multiple cases to the same logic

```java
case 1:
case 2:
case 3:
    handleSmall();
    break;
```

---

### 4. `switch` vs `if-else`

Prefer `switch` when:  
• checking one variable against many constant values  
• values are discrete and finite

Prefer `if-else` when:  
• using ranges (`>`, `<`, `>=`)  
• combining multiple variables  
• using complex boolean logic

---

### 5. Enhanced `switch` (Java 14+)

Enhanced `switch` introduces:  
• arrow syntax (`->`)  
• no fall-through by default  
• expression form with return value

```java
switch (day) {
case MONDAY -> work();
case SATURDAY, SUNDAY -> rest();
default -> unknown();
}
```

```java
switch (month.toLowerCase()) {  
    case "jan", "feb", "mar" -> {  
        return "1st";  
    }  
    case "apr", "may", "jun" -> {  
        return "2nd";  
    }  
    case "sep", "jul", "aug" -> {  
        return "3rd";  
    }  
    case "oct", "nov", "dec" -> {  
        return "4th";  
    }  
    default ->  {  
        return null;  
    }  
}
```
Rules:  
• no `break` required  
• each case is isolated  
• safer and more readable than traditional `switch`

---
### 6. `switch` Expression (Returns a Value)

A `switch` expression produces a value.

```java
int score = switch (grade) {
case "A" -> 90;
case "B" -> 80;
default -> 0;
};
```

Rules  
• every possible path must yield a value  
• `default` is required unless all cases are covered

---
### 7. `yield` Keyword (Multi-Statement Case)

Use `yield` when a case requires multiple statements.

```java
String result = switch (month.toLowerCase()) {  
    case "jan", "feb", "mar" -> {  
        yield "1st";  
    }  
    case "apr", "may", "jun" -> {  
        yield "2nd";  
    }  
    case "sep", "jul", "aug" -> {  
        yield "3rd";  
    }  
    case "oct", "nov", "dec" -> {  
        yield "4th";  
    }  
    default ->  {  
        yield null;  
    }  
};  
return result;
```

<span style="color:rgb(255, 0, 0)">`yield` exits the switch expression with a value.</span>

---
### 8. Common Pitfalls

• Using `switch` for ranges (wrong tool)  
• Forgetting `break` in traditional `switch`  
• Mixing statement and expression styles  
• Overusing `default` to hide missing cases

---

### 9. Mental Checklist

Before using `switch`:  
• Is the variable discrete and finite?  
• Are cases constants?  
• Do I need a value or just side effects?  
• Would enhanced `switch` be safer?

---

### 10. Summary

• Traditional `switch` allows fall-through  
• Enhanced `switch` removes fall-through by default  
• `switch` expression returns a value  
• `yield` supports multi-statement cases  
• `switch` improves clarity when used for fixed-option selection

---
## 4. Loops

### for loop

#### Canonical form

```java
for (initialization; condition; update) {
    // loop body
}
```

- **initialization**: executed once before the loop starts (typically a counter setup).
    
- **condition**: evaluated before each iteration; if `false`, the loop terminates.
    
- **update**: executed after each iteration of the loop body.
    

#### Common variations

- **Omitting parts**:
    
    - `for (; condition; )` behaves like a `while` loop.
        
    - `for (;;)` creates an infinite loop (must be exited via `break` or `return`).
        
- **Multiple variables** (frequent in two-pointer patterns):
    
    - `for (int l = 0, r = n - 1; l < r; l++, r--) { ... }`
        
- **Enhanced for (for-each)**:
    
    - `for (int x : array) { ... }`
        
    - Best for read-only traversal; do not structurally modify collections inside a for-each loop.
        

#### Typical pitfalls

- **Off-by-one errors**: `i < n` vs `i <= n - 1`.
    
- **Incorrect update placement**: modifying loop variables inside the body unintentionally.
    
- **Side effects in conditions**: avoid complex logic inside the condition unless intentional.
    
- **Floating-point comparison**: never rely on `!=` or `==` for floating-point loop termination.
    

#### Flow control

- `break`: exits the current loop.
    
- `continue`: skips the rest of the current iteration.
    
- **Labeled break / continue**: exits or continues an outer loop in nested structures.
    

---

### while loop

#### Syntax

```java
while (condition) {
    // loop body
}
```

- The condition is evaluated before each iteration.
    
- The loop body may execute zero times if the condition is initially false.
    

#### Usage characteristics

- Ideal when the number of iterations is not known in advance.
    
- Common in input processing, pointer movement, and state-driven loops.
    

#### Key considerations

- Always ensure the loop state progresses toward termination.
    
- Prefer readable conditions; extract complex logic into boolean variables or methods.
    

---

### do-while loop

#### Syntax

```java
do {
    // loop body
} while (condition);
```

- The loop body executes at least once.
    
- The condition is evaluated after each iteration.
    

#### Typical use cases

- Menu-driven programs.
    
- Retry mechanisms.
    
- Validation logic that must run at least once.
    

#### Common pitfalls

- Forgetting the trailing semicolon after the `while` condition.
    

---

### Choosing the right loop

- **Fixed or countable iterations** → `for`
    
- **Condition-driven or input-based loops** → `while`
    
- **Must execute at least once** → `do-while`
    

---

### Terminating a loop

Loop termination defines **when and how a loop stops executing**. A correct termination strategy is essential to avoid infinite loops, missed edge cases, or premature exits.

---

#### Natural termination (condition becomes false)

A loop ends automatically when its condition evaluates to `false`.

```java
int i = 0;
while (i < 5) {
    // Loop terminates naturally when i reaches 5
    i++;
}
```

Key idea:

- The loop variable **must make progress** toward falsifying the condition.
    

---

#### `break` – force immediate termination

`break` exits the **current loop immediately**, regardless of the loop condition.

```java
for (int i = 0; i < nums.length; i++) {
    if (nums[i] == target) {
        break; // Stop the loop once the target is found
    }
}
```

Characteristics:

- Exits only the **nearest enclosing loop**
    
- Common in search, validation, and error-detection logic
    

---

#### `continue` – skip current iteration

`continue` does **not** terminate the loop. It skips the remaining statements in the current iteration and proceeds to the next one.

```java
for (int x : nums) {
    if (x < 0) continue; // Skip invalid data
    sum += x;
}
```

Effect by loop type:

- `for`: jumps to the update expression, then re-checks the condition
    
- `while` / `do-while`: jumps directly to the condition check
    

---

#### Labeled termination (nested loops)

Labels allow terminating or continuing an **outer loop** from within nested loops.

```java
search:
for (int r = 0; r < grid.length; r++) {
    for (int c = 0; c < grid[r].length; c++) {
        if (grid[r][c] == target) {
            break search; // Exit both loops immediately
        }
    }
}
```

Use cases:

- Multi-dimensional search
    
- Early abort from deeply nested logic
    

---

#### Common termination pitfalls

- Loop variable is never updated → infinite loop
    
- `continue` skips the update step in `while` loops
    
- Termination condition relies on floating-point equality
    
- Overusing `break` / `continue` reduces readability
    

---

#### Termination checklist

Before finalizing a loop, verify:

- What condition guarantees termination?
    
- Is the loop state always progressing?
    
- Can `break` be triggered unexpectedly?
    
- Are all exit paths intentional and documented?

---
### Debug checklist

Before writing a loop, verify:

1. What invariant must hold true after each iteration?
    
2. Is the termination condition reachable?
    
3. Are edge cases handled (empty input, single element, last iteration)?
    
4. Is collection modification safe (Iterator vs for-each)?

## 5. Variable Scope

Variable scope defines **where a variable name is visible and legally accessible in source code**. In Java, scope rules are enforced at **compile time** and are based on **lexical (static) scoping**, not runtime behavior.

---

### 5.1 Block Scope

A _block_ is any code enclosed by `{}`. Java introduces a new scope for each block.

```java
if (condition) {
    int x = 10; // x is visible only inside this block
}
// x is not accessible here
```

Blocks that create scope include:

- `if / else`
    
- `for`, `while`, `do-while`
    
- standalone `{}` blocks
    
- method bodies
    

Key rules:

- Variables declared inside a block are **not visible outside** that block.
    
- Inner blocks can access variables declared in outer blocks.
    
- Sibling blocks cannot see each other’s variables.
    

```java
int a = 5;
for (int i = 0; i < 3; i++) {
    int b = i;
    // a and b are visible here
}
// i and b are out of scope here
```

---

### 5.2 Lifetime

Lifetime describes **how long a variable exists during program execution**.

For _local variables_:

- Lifetime begins when the variable is declared and initialized.
    
- Lifetime ends when the enclosing block finishes execution.
    
- Storage is typically allocated on the **stack frame** of the method.
    

Important distinction:

- **Scope** determines _where_ a variable can be referenced.
    
- **Lifetime** determines _whether_ the variable still exists in memory.
    

```java
if (true) {
    int temp = 42;
    // temp exists and is accessible here
}
// temp no longer exists (out of lifetime)
```

Although scope and lifetime often coincide for local variables, they are **conceptually different** and diverge for fields and objects.

---

### 5.3 Variable Shadowing

_Shadowing_ occurs when a variable declared in an inner scope has the **same name** as a variable in an outer scope.

```java
int value = 10;

if (true) {
    int value = 5; // shadows outer variable
    // value refers to the inner variable (5)
}
// value refers to the outer variable (10)
```

Characteristics:

- Shadowing is resolved at **compile time**.
    
- The outer variable becomes temporarily inaccessible within the inner scope.
    
- No compilation error is produced.
    

Risks:

- Reduced readability
    
- High likelihood of logical errors
    
- Difficult debugging in deeply nested code
    

Best practices:

- Avoid reusing variable names across nested scopes.
    
- Prefer short-lived, narrowly scoped variables with descriptive names.
    

---

### 5.4 Scope in Control Flow Constructs

Loop variables have scope limited to the loop body.

```java
for (int i = 0; i < 5; i++) {
    // i is accessible here
}
// i is not accessible here
```

In contrast, variables declared _before_ the loop remain visible after the loop:

```java
int i = 0;
while (i < 5) {
    i++;
}
// i is still accessible here
```

This distinction is critical when deciding where to declare loop counters and accumulators.

---

### 5.5 Scope Design Guidelines

- Minimize scope: declare variables as close as possible to their first use.
    
- Avoid shadowing, especially for method parameters and fields.
    
- Use block scope intentionally to limit variable visibility.
    
- Treat scope as a tool for correctness, not just organization.
    

---

### 5.6 Common Scope-Related Errors

- Attempting to access variables outside their block
    
- Accidentally shadowing a variable and modifying the wrong one
    
- Declaring variables too early, increasing their visible scope unnecessarily
    

Understanding scope is essential for writing **predictable, maintainable, and correct Java programs**, and it forms the foundation for object state, method design, and memory reasoning.

---
## Control Flow — Summary

Control flow defines **the execution order of statements** in a program. Through conditionals and loops, Java provides structured mechanisms to control branching, repetition, and termination.

Key takeaways:

- Conditional logic (`if`, `switch`) determines _which path_ is executed.
    
- Loops (`for`, `while`, `do-while`) determine _how long_ execution continues.
    
- Correct loop termination is critical for program correctness and predictability.
    
- Readable control flow minimizes hidden state and reduces debugging complexity.
    

A well-designed control flow ensures that program behavior is explicit, bounded, and easy to reason about.

---

## 6. Console Input with Scanner

Console input introduces **external state** into a program. Unlike internal variables, user input is inherently unreliable and must be handled defensively.

The `Scanner` class provides a high-level abstraction for reading and parsing input from various sources, most commonly `System.in`.

> <span style="color:rgb(255, 0, 0)">Not suitable for reading files, use Files or BufferReader</span>. Scanner is so goddamn slow.

---

### 6.1 Purpose and Characteristics

- Reads input as tokens separated by delimiters (whitespace by default).
    
- Converts raw character input into typed values (e.g., `int`, `double`, `String`).
    
- Commonly used for simple command-line programs and learning environments.
    

Scanner prioritizes **convenience over performance** and is not intended for high-throughput input scenarios.

---

### 6.2 Creating a Scanner

```java
Scanner scanner = new Scanner(System.in);
```

- `System.in` represents the standard input stream.
- The Scanner instance maintains its own internal buffer.
- Only one Scanner should typically wrap `System.in` within a program.

---

### 6.3 Token-Based Input Methods

Commonly used methods:

- `nextInt()` — reads the next integer token
    
- `nextDouble()` — reads the next floating-point token
    
- `next()` — reads the next non-whitespace token
    
- `nextLine()` — reads an entire line
    

Important semantic distinction:

- Token-based methods stop at delimiters.
- `nextLine()` consumes the remainder of the current line, including empty input.
- `nextInt()` and `nextDouble()` needs another `nextLine()` to consume the "\n" at each end of the input line.

---

### 6.4 Input Buffer Behavior

Mixing token-based methods with `nextLine()` can lead to unexpected behavior due to leftover newline characters in the buffer.

Typical pattern:

- After calling `nextInt()` or similar, explicitly consume the remaining newline before calling `nextLine()`.
    

Understanding buffer state is essential for writing correct interactive programs.

---

### 6.5 Input Validation

All user input should be treated as untrusted.

Validation strategies:

- Existence checks using `hasNextInt()`, `hasNextDouble()`
- Range validation after parsing
- Semantic validation based on program requirements
- Use `Scanner.next()` to consume the invalid token if failed.

Robust input handling prevents runtime exceptions and ensures predictable control flow.

---

### 6.6 Resource Management

Scanner implements `AutoCloseable` and should be closed when no longer needed.

```java
scanner.close();
```

When wrapping `System.in`, closing the Scanner also closes the underlying input stream. In long-running programs, this should be done with care.

---

### 6.7 Design Considerations

- Avoid embedding input logic deep inside business logic.
    
- Separate input parsing from computation when possible.
    
- Treat Scanner as a boundary-layer utility, not a core dependency.
    

Console input marks the transition from deterministic programs to **interactive systems**, where control flow must account for invalid, unexpected, or delayed input.

---