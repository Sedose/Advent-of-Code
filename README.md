# Advent-of-Code
Single Markdown file holding my solutions for Advent of Code

## 2025

### Day 1. Part 1

#### The code
```Kotlin

val input =
"""
R22
L2
R13
L49
...
L7
L12
L35
R50
"""

fun main() {
    input.trimIndent()
        .lines()
        .map(String::trim)
        .filter(String::isNotEmpty)
        .let(::calculatePassword)
        .let(::println)
}

const val dialUpperBound = 100

fun calculatePassword(input: List<String>): Int =
    input.map(::parseMoveDelta)
        .scan(50) { position, delta ->
            // Forces position to stay within [`start`, `end`] range
            // which represents a closed dial
            (position + delta).mod(dialUpperBound)
        }
        .count(0::equals)

fun parseMoveDelta(text: String): Int {
    val amount = text.drop(1).toInt()
    return when (text.first()) {
        'L' -> -amount
        'R' -> amount
        else -> error("Unexpected move instruction")
    }
}
```

#### **Style**
* **Pipeline programming**
* **Data-oriented programming**
* **Functional-like programming**
* **Declarative-like programming**
* **Side-effect isolation** (I/O only in `main`)
* **Expression-oriented Kotlin**
* **Structured, composable functions**
* **No mutable global state**
* **Pure functions**
* **Minimal branching, maximal transformation**
* **Clean, predictable control flow**
* **Lean functional patterns**
