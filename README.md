# Advent-of-Code

Single Markdown file holding my solutions for Advent of Code
## **Programming style, code characteristics, philosophy**
* **Pipeline programming**
* **Data-oriented programming**
* **Functional-like programming**
* **Declarative-like programming**
* **Side-effect isolation** (I/O only in `main`)
* **Expression-oriented programming**
* **Structured, composable functions**
* **No mutable global state**
* **Prefer not to have even local mutable state unless absolutely needed to gain smth much more valuable**
* **Pure functions**
* **Minimal branching, maximal transformation**
* **Clean, predictable control flow**
* **Lean functional patterns**
* **Less code means better. Code is dense**
* **The code is not opimized to gain maximum raw speed**
* **The code is kept vertical, so it stays within a regular monitor size, no need for endless zooming, scroling nonsense**
* **The code is opimized for correctness, simplicity and clarity**
* **The code is like do 1, do 2, do 3, get result, without stupid or smart nonsense**

## 2025

### Day 1. Part 1

#### The code

##### Kotlin

```Kotlin
package org.example

private const val DIAL_UPPER_BOUND = 100

fun main() {
    (
        object {}.javaClass
            .getResource("/day1_part1.txt")
            ?.readText()
            ?: error("Resource not found")
        )
        .lineSequence()
        .map(String::trim)
        .filter(String::isNotEmpty)
        .map(::parseMoveDelta)
        .scan(50, ::nextPosition)
        .count { it == 0 }
        .let(::println)
}

fun parseMoveDelta(text: String): Int {
    val amount = text.drop(1).toInt()
    return when (text.first()) {
        'L' -> -amount
        'R' -> amount
        else -> 0
    }
}

fun nextPosition(position: Int, delta: Int): Int =
    (position + delta).mod(DIAL_UPPER_BOUND)
```

##### Scala

```Scala
import scala.io.Source
import scala.util.Using
import scala.util.chaining.scalaUtilChainingOps

private val DIAL_UPPER_BOUND = 100

@main def main(): Unit =
  Using.resource(Source.fromResource("day1_part1.txt"))(_.mkString)
    .linesIterator
    .map(_.trim)
    .filter(_.nonEmpty)
    .map(parseMoveDelta)
    .scanLeft(50)(nextPosition)
    .count(_ == 0)
    .pipe(println)

def parseMoveDelta(text: String): Int =
  val amount = text.drop(1).toInt
  text.head match
    case 'L' => -amount
    case 'R' => amount
    case _ => 1

def nextPosition(position: Int, delta: Int): Int =
  Math.floorMod(position + delta, DIAL_UPPER_BOUND)
```

##### Java

```Java
import static com.example.Input.INPUT;

record DialState(int position, int zeroHits) {}

void main() {

    int dialUpperBound = 100;

    int password =
        INPUT.stripIndent()
            .lines()
            .map(String::trim)
            .filter(line -> !line.isEmpty())
            .map(this::parseMoveDelta)
            .reduce(
                new DialState(50, 0),
                (state, delta) -> nextDialState(state, delta, dialUpperBound),
                (_, right) -> right
            )
            .zeroHits();

    IO.println(password);
}

private int parseMoveDelta(String text) {
    int amount = Integer.parseInt(text.substring(1));
    return switch (text.charAt(0)) {
        case 'L' -> -amount;
        case 'R' -> amount;
        default -> 0;
    };
}

private DialState nextDialState(DialState state, int delta, int dialUpperBound) {
    int position = Math.floorMod(state.position() + delta, dialUpperBound);
    int zeroHits = state.zeroHits() + (position == 0 ? 1 : 0);
    return new DialState(position, zeroHits);
}
```

##### Rust

```Rust
use std::ops::Not;

pub const INPUT: &str = r#"
R22
R26
L20
R20
---
L12
L35
R50
"#;

const DIAL_UPPER_BOUND: i32 = 100;

fn main() {
    let password = INPUT.lines()
        .map(str::trim)
        .filter(|line| line.is_empty().not())
        .map(parse_move_delta)
        .scan(50, |position, delta| {
            *position = (*position + delta).rem_euclid(DIAL_UPPER_BOUND);
            Some(*position)
        })
        .filter(|position| *position == 0)
        .count();

    println!("{password}");
}

fn parse_move_delta(text: &str) -> i32 {
    let (direction, amount_text) = text.split_at(1);
    let amount: i32 = amount_text.parse().unwrap_or(0);

    match direction {
        "L" => -amount,
        "R" => amount,
        _ => 0,
    }
}
```

##### Gleam

```Gleam
const input = "
R22
L2
R13
L49
...
L7
L12
L35
R50
"

pub fn main() {
  input
  |> string.split("\n")
  |> list.map(string.trim)
  |> list.filter(fn(s) { s != "" })
  |> calculate_password
  |> int.to_string
  |> io.println
}

const dial_upper_bound = 100

fn calculate_password(input: List(String)) -> Int {
  input
  |> list.map(parse_move_delta)
  |> list.scan(50, next_position)
  |> list.count(fn(pos) { pos == 0 })
}

fn next_position(position, delta) -> Int {
  let new_pos = position + delta
  new_pos % dial_upper_bound
}

fn parse_move_delta(text: String) -> Int {
  let amount = 
    text
    |> string.drop_start(1)
    |> int.parse
    |> result.unwrap(0)
  
  case string.first(text) {
    Ok("L") -> -amount
    Ok("R") -> amount
    _ -> panic as "Unexpected move instruction"
  }
}
```



### Day 1. Part 2

#### The code

##### Kotlin

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

##### Gleam

```Gleam

const input = "
R22
L2
R13
L49
...
L7
L12
L35
R50
"

const dial_upper_bound = 100

const dial_initial_position = 50

pub fn main() {
  input
  |> string.split("\n")
  |> list.map(string.trim)
  |> list.filter(fn(line) { !string.is_empty(line) })
  |> calculate_password
  |> int.to_string
  |> io.println
}

fn calculate_password(input: List(String)) -> Int {
  input
  |> list.flat_map(parse_clicks)
  |> list.scan(dial_initial_position, next_position)
  |> list.count(fn(pos) { pos == 0 })
}

fn next_position(position, delta) -> Int {
  let new_pos = position + delta
  new_pos % dial_upper_bound
}

fn parse_clicks(line: String) -> List(Int) {
  let direction = case string.first(line) {
    Ok("L") -> -1
    Ok("R") -> 1
    _ -> 0
  }

  let amount = string.drop_start(line, 1) |> int.parse |> result.unwrap(0)

  list.repeat(direction, amount)
}
```

##### Rust

```Rust
use std::iter::repeat;
use std::ops::Not;

pub const INPUT: &str = r#"
R22
R26
L20
R20
---
L12
L35
R50
"#;

const DIAL_UPPER_BOUND: i32 = 100;
const INITIAL_POSITION: i32 = 50;

fn main() {
    let password = INPUT
        .lines()
        .map(str::trim)
        .filter(|line| line.is_empty().not())
        .flat_map(parse_move_deltas)
        .scan(INITIAL_POSITION, |position, delta| {
            *position = (*position + delta).rem_euclid(DIAL_UPPER_BOUND);
            Some(*position)
        })
        .filter(|position| *position == 0)
        .count();

    println!("{password}");
}

fn parse_move_deltas(text: &str) -> impl Iterator<Item = i32> {
    let (direction_text, amount_text) = text.split_at(1);
    let amount = amount_text.parse().unwrap_or(0);

    let direction = match direction_text {
        "L" => -1,
        "R" => 1,
        _ => 0,
    };

    repeat(direction).take(amount)
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

### Day 2. Part 2

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
    return id in (id + id).drop(1).dropLast(1)
}
```

### Day 3. Part 1

#### The code
```Kotlin
fun main() {
    input.lineSequence()
        .sumOf(::maximumTwoDigitValue)
        .let(::println)
}

fun maximumTwoDigitValue(bank: String): Int {
    val numbers = bank.map { it.digitToInt() }
    val firstMax = numbers.dropLast(1).max()
    val indexOfFirstMax = numbers.indexOf(firstMax)
    val secondMax = numbers.drop(indexOfFirstMax + 1).max()
    return firstMax * 10 + secondMax
}
```
