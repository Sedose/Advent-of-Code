# Advent-of-Code
Single Markdown file holding my solutions for Advent of Code

## 2025

## **Programming style, code characteristics**
* **Pipeline programming**
* **Data-oriented programming**
* **Functional-like programming**
* **Declarative-like programming**
* **Side-effect isolation** (I/O only in `main`)
* **Expression-oriented Kotlin**
* **Structured, composable functions**
* **No mutable global state**
* **Prefer not to have even local mutable state unless absolutely needed to gain smth more valuable**
* **Pure functions**
* **Minimal branching, maximal transformation**
* **Clean, predictable control flow**
* **Lean functional patterns**
* **Less code means better. Code is dense**
* **The code is not opimized to gain maximum raw speed**
* **The code is opimized for correctness, simplicity and clarity**

### Day 1. Part 1

#### The code
```Kotlin

const val input =
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

### Day 1. Part 2

#### The code
```Kotlin
const val input =
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
""".trimIndent()
    .lines()
    .map(String::trim)
    .filter(String::isNotEmpty)

const val dialUpperBound = 100

const val initialPosition = 50

fun main() {
    input.let(::calculatePassword)
        .let(::println)
}

fun calculatePassword(input: List<String>): Int =
    input.asSequence()
        .flatMap(::parseClicks)
        .scan(initialPosition) { position, delta ->
            (position + delta).mod(dialUpperBound)
        }
        .count { it == 0 }


fun parseClicks(text: String): Sequence<Int> {
    val direction =
        when (text.first()) {
            'L' -> -1
            'R' -> 1
            else -> error("Unexpected move instruction")
        }
    val amount = text.drop(1).toInt()
    return generateSequence { direction }.take(amount)
}
```

### Day 2. Part 1

#### The code
```Kotlin
fun main() {
    input.splitToSequence(",")
        .map { rawRange -> rawRange.split("-") }
        .map { (startInclusive, endInclusive) ->
            startInclusive.toLong()..endInclusive.toLong()
        }
        .flatMap { it.asSequence() }
        .filter(::isInvalidId)
        .sum()
        .let(::println)
}

fun isInvalidId(id: Long): Boolean {
    val id = id.toString()
    val halfLength = id.length / 2
    return id.take(halfLength) == id.drop(halfLength)
}
```
