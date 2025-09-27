---
layout: post
title: "My First Scala Application: Console Calculator"
date: 2025-09-26
categories: [ Scala, Beginner Guide ]
image: /assets/2025-09-26/thumbnail.jpg
---

As an experienced Java developer, I recently decided to dive into the world of Scala to expand my programming toolkit.
Scala’s unique blend of object-oriented and functional programming intrigued me, so I decided to create a simple
command-line application — a Calculator — to get familiar with the language.

In this post, I’ll share my experience building this program and highlight some key differences between Java and Scala.
If you’re also a Java developer curious about Scala, this guide might help you take your first steps.

## Prerequisites

Here are the tools and knowledge I relied on to set up my development environment:

- **Java Development Kit (JDK)** installed on your system.
- **Scala** installed. You can download Scala from its [official website](https://scala-lang.org/).
- **SBT (Scala Build Tool)**: SBT is a popular build tool for Scala, akin to Maven or Gradle for Java. Download it from its [official website](https://www.scala-sbt.org/).
- Familiarity with concepts like classes, objects, data types, and control flow from Java.
- Basics of Scala 3 which I read in the [Official Scala 3 Book](https://docs.scala-lang.org/scala3/book/introduction.html) to understand the language's syntax and key concepts.

## Analyze the Problem

The goal is to create a command-line calculator application that processes mathematical expressions in the form of
`<number><operator><number>` with optional spaces. For example, `5+3` or `4.1 ^ 2`.

It will support basic operations: addition, subtraction, multiplication, division, and exponents. The app will handle
invalid inputs gracefully, prevent division by zero, and allow users to exit by entering `:q`.

The interaction will happen using a read-evaluate-print loop.

## Steps to create the Calculator

### Create the project structure

1. To get started, create a new directory for your project:
    ```bash
    mkdir console-calculator
    cd console-calculator
    ```
2. In the directory, create the following structure:
    ```
    console-calculator/
    ├── build.sbt
    └── src/
        └── main/
            └── scala/
                └── com/
                    └── study/
                        ├── Calculator.scala
                        ├── Main.scala
                        └── Operation.scala
    ```

3. Use `build.sbt` to define dependencies and project settings. Here’s an example of this project:
    ```scala
    name := "console-calculator"
    version := "0.1.0-SNAPSHOT"
    scalaVersion := "3.3.6"
    ```

### Implement the logic

### Step 1. Create a simple **Read-Eval-Print Loop (REPL)**

In this step we will implement the following logic:
- The REPL will read the user input line-by-line.
- The program will exit when the user types `:q`.
- It will simply echo back the typed input for now.

```scala
import scala.annotation.tailrec
import scala.io.StdIn.readLine

val LinePrefix = "calc>"

@main def app(): Unit =
  println("Welcome to Console Calculator!")
  repl()

@tailrec
def repl(): Unit =
  println("Type anything, or type :q to quit.")
  val input = readLine(LinePrefix)

  if input == null || input.equals(":q") then
    println("Bye!")
  else
    println(s"You typed: $input")
    repl()
```

### Step 2. Validate Input for Calculator Operations

In this step, we extend the **REPL** to validate user inputs:
- Only mathematical operations such as addition (`+`), subtraction (`-`), multiplication (`*`), division (`/`), and power (`^`) will be accepted.
- Inputs outside the allowed format will trigger an **invalid input error message**, and the program will prompt the user to try again.

We will use a **regular expression** to validate inputs. This will ensure the input is either:
1. A valid mathematical expression (e.g., `5 + 3`, `7 * 4`),
2. Or the exit command `:q`.

```scala
import com.study.calculator.console.Calculator.calculate

import scala.annotation.tailrec
import scala.io.StdIn.readLine
import scala.util.matching.Regex

val LinePrefix = "calc>"
val validInputPattern: Regex = """^\s*\d+(\s*[\+\-\*/\^]\s*\d+)+\s*$""".r // Pattern for valid expressions

@main def app(): Unit =
  println("Welcome to Console Calculator!")
  repl()

@tailrec
def repl(): Unit =
  println("Type an operation. Examples: 1 + 2 | 4.1 - 1 | 2 * 3 | 6 / 2 | 3.2 ^ 2")
  val input = readLine(LinePrefix)

  if input == null || input.equals(":q") then
    println("Bye!")
  else if input.trim.isEmpty then
    repl()
  else
    val result = input.trim match
      case Pattern(a, operation, b) => calculate(BigDecimal(a), operation, BigDecimal(b))
      case other => Left("Invalid input!")
    result.fold(error => println(error), res => println(s"Result is [$res]"))
    repl()
```
The syntax where we validate and extract the user input with `match` and `case` keywords is called pattern matching. 
It is in a way like Java `switch` operator, but it is much more powerful. 
It allows you to match against types, regex, and case classes.

You can notice that `Main.scala` now calls `Calculator.calculate`.
Let's add this class with a stub implementation.

```scala
object Calculator:

  def calculate(a: BigDecimal, operation: String, b: BigDecimal): Either[String, BigDecimal] =
    Left("Not implemented yet!")
```
`Either` is Scala way to gracefully handle exceptions. Instead of throwing them, it allows returning either the result or error message.

At this point, `Main.scala` is ready, because we have all the logic needed to handle user's input.
The attempt to make an actual calculation will return a message saying that the logic is not implemented yet.
This is expected.

Before we move on and implement the calculation, our application needs to know what an operation is.
Let's define the list of support operations in the next section.

### Step 3. Introduce operations
An operation is the action performed on two operands. It is a core entity of our Calculator.
Let's add an enum for this and declare operations for addition, subtraction, multiplication, division, and power.

```scala
enum Operation:
  case Add, Subtract, Multiply, Divide, Power

object Operation:
  def fromString(input: String): Either[String, Operation] =
    input match
      case "+" => Right(Add)
      case "-" => Right(Subtract)
      case "*" => Right(Multiply)
      case "/" => Right(Divide)
      case "^" => Right(Power)
      case other => Left(s"Unknown operation [$other]")
```
The `case` instruction declares which operation types we are going to support for calculations.

Object `Operation` is the companion object for our enum. It allows defining useful manipulations with the data, such as 
`fromString` in our case. This method is similar to Java static methods, as it is related to the enum in general, not to its specific instance.

Now that we have the operation list in place, we can proceed to implement the calculation logic.

### Step 4. Implement calculation

In this step, we implement the core **calculation logic** within the `Calculator` object.

```scala
import com.study.calculator.console.Operation.{Add, Divide, Multiply, Power, Subtract}

object Calculator:

  def calculate(a: BigDecimal, operation: String, b: BigDecimal): Either[String, BigDecimal] =
    Operation.fromString(operation) match
      case Right(value) => calculate(a, value, b)
      case Left(error) => Left(error)

  def calculate(a: BigDecimal, operation: Operation, b: BigDecimal): Either[String, BigDecimal] =
    operation match
      case Add => Right(a + b)
      case Subtract => Right(a - b)
      case Multiply => Right(a * b)
      case Divide =>
        if b == 0 then Left("Cannot divide by zero!")
        else Right(a / b)
      case Power =>
        if b.isValidInt then
          Right(a.pow(b.toInt))
        else
          Left("Please enter an integer exponent!")
```

Here, we are declaring `Calculator` as an object, which means it is the singleton instance of its class.

It has two overloaded methods `calculate`.
The first one is for convenience of converting `String` to `Operation`. This convertion is done with the method we have 
already added as `Operation.fromString` earlier. Once this conversion is done, this method calls the second method.

The second `calculate` method handles the actual calculations for all the operation types.
It validates the case of division by zero. For simplicity, the power operation is limited to integer exponents.

### Step 5. Add Unit Tests

Unit tests are essential and cannot be emphasized enough.

To keep the article concise, I will omit putting them here. Feel free to check them on
[my GitHub page](https://github.com/bohdanmartines/console-calculator).

### Step 6. Test the application

We have everything in place to run our application and verify how it works.
Let's open the terminal in the root directory and run `sbt run`:

```bash
  >sbt run
  Welcome to Console Calculator!
  Type an operation. Examples: 1 + 2 | 4.1 - 1 | 2 * 3 | 6 / 2 | 3.2 ^ 2
  calc>
```

You can see the welcome message.
Now, let's perform a couple of calculations:

```bash
  calc>3.12 + 2.23 
  Result is [5.35]
  Type an operation. Examples: 1 + 2 | 4.1 - 1 | 2 * 3 | 6 / 2 | 3.2 ^ 2
  calc>9.4/2
  Result is [4.7]
  Type an operation. Examples: 1 + 2 | 4.1 - 1 | 2 * 3 | 6 / 2 | 3.2 ^ 2
  calc>
```

Superb, it works!
To leave the application, simply type `:q`:

```bash
  calc>:q
  Bye!
```

## Conclusion
Today, we built a complete console calculator application in Scala 3. We explored several key concepts:
- **Functional programming patterns** using `Either` for error handling
- **Object** and **Companion Object** concepts
- **Pattern matching** for clean operation handling
- **Input validation** with regular expressions

If you want to check out the repository and try it on your machine, you are more than welcome to get the code 
[here](https://github.com/bohdanmartines/console-calculator).

This is my first attempt to dive into the world of Scala. More articles will come.
See you next time, and happy coding!
