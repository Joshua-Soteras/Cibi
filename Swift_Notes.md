# Swift Notes

LLVM = Low Level Virtual Machine

---

## Python vs Swift (High Level)

| | Python | Swift |
|---|---|---|
| Typing | Dynamic | Static (with inference) |
| Memory | Garbage Collector (GC) | ARC (Automatic Reference Counting) |
| Execution | Interpreted | Compiled via LLVM |
| Paradigm | Multi-paradigm | Multi-paradigm |

---

## Swift History
- 2014: Introduced by Apple
- Replacement for Objective-C

---

## How Swift Works

- **Statically Typed** — types are checked at compile time, not runtime
- **Type Inference** — Swift infers types like Python feels, but enforces them at compile time
- **Automatic Reference Counting (ARC)** — memory management done by the compiler
  - Tracks how many references point to an object
  - Deallocates when count hits zero
  - All happens at compile time (unlike GC which runs at runtime)
  - Weakness: **Strong Reference Cycles** — two objects referencing each other, count never hits zero, memory leaks
  - Fix: use `weak` or `unowned` references
- **LLVM** — compiler backend that optimizes and compiles Swift to machine code

---

## Variables & Types

```swift
var name = "Josh"        // mutable
let pi = 3.14            // immutable — like Python's convention but enforced

var age: Int = 25        // explicit type
var score: Double = 9.5
var isActive: Bool = true
var label: String = "hello"
```

> `let` is the default mindset in Swift — only use `var` when mutation is needed. Aligns with immutability preference in FP.

**Type inference** works like Python's duck typing feel, but the type is locked at compile time:
```swift
var x = 42       // inferred as Int, cannot reassign a String later
```

---

## Optionals

Optionals represent a value that may or may not exist — Swift's compile-time enforced version of `None`.

```swift
var username: String? = nil     // Optional String
var username: String? = "Josh"
```

**Unwrapping options:**

```swift
// 1. if let — safe unwrap
if let name = username {
    print(name)
}

// 2. guard let — early exit pattern (common in functions)
guard let name = username else { return }

// 3. nil coalescing — like Python's `or` default
let display = username ?? "Guest"

// 4. force unwrap — crash if nil, avoid unless certain
let name = username!
```

> Think of optionals like a `Maybe` type in FP — forces you to handle the None case explicitly rather than letting it blow up at runtime like Python.

---

## Functions

```swift
func greet(name: String) -> String {
    return "Hello, \(name)"
}

greet(name: "Josh")
```

**Named parameters** — like Python kwargs, but required by default at the call site.

**External vs internal parameter labels:**
```swift
func log(food item: String) { }   // external: food, internal: item
log(food: "Apple")
```

**Default parameters** — same as Python:
```swift
func greet(name: String = "Guest") -> String { ... }
```

**Variadic parameters** — like `*args`:
```swift
func sum(_ numbers: Int...) -> Int {
    numbers.reduce(0, +)
}
```

**Multiple return values** — use tuples, like Python:
```swift
func minMax(_ arr: [Int]) -> (min: Int, max: Int) {
    return (arr.min()!, arr.max()!)
}
let result = minMax([1, 2, 3])
result.min   // 1
result.max   // 3
```

---

## Closures

Closures are Swift's first-class functions — equivalent to Python lambdas but far more capable.

```swift
// Full closure syntax
let add = { (a: Int, b: Int) -> Int in
    return a + b
}

// Shorthand (inferred types, implicit return)
let add: (Int, Int) -> Int = { $0 + $1 }
```

**Trailing closure syntax** — when the last argument is a closure, it moves outside the parens:
```swift
nums.sorted(by: { $0 < $1 })
nums.sorted { $0 < $1 }        // trailing closure form
```

**Map / Filter / Reduce** — same concept as Python/FP:
```swift
let nums = [1, 2, 3, 4, 5]

nums.map { $0 * 2 }               // [2, 4, 6, 8, 10]
nums.filter { $0 > 2 }            // [3, 4, 5]
nums.reduce(0) { $0 + $1 }        // 15
nums.compactMap { ... }           // like map but drops nils — useful with optionals
```

> Closures capture their surrounding context (variables) — same as Python closures. `@escaping` is required when the closure outlives the function call (e.g., async callbacks).

---

## Collections

### Array
```swift
var fruits = ["apple", "banana"]
fruits.append("mango")
fruits[0]                          // "apple"
fruits.count
fruits.isEmpty
fruits.contains("apple")
fruits.sorted()
fruits.filter { $0.hasPrefix("a") }
```

### Dictionary
```swift
var scores: [String: Int] = ["Josh": 95, "Alex": 88]
scores["Josh"]                     // Optional(95) — always returns Optional
scores["Josh", default: 0]         // 95 — safe with default
scores["New"] = 70                 // insert or update
scores.keys
scores.values
```

### Set
```swift
var tags: Set<String> = ["swift", "ios", "mobile"]
tags.insert("apple")
tags.contains("ios")               // true
tags.union(otherSet)
tags.intersection(otherSet)
```

---

## Structs vs Classes

This is one of Swift's most important distinctions.

| | Struct | Class |
|---|---|---|
| Type | Value type (copied) | Reference type (shared pointer) |
| Inheritance | No | Yes |
| ARC | No | Yes |
| Default in Swift | Preferred | Use when sharing state |

```swift
struct Point {
    var x: Int
    var y: Int
}

class User {
    var name: String
    init(name: String) { self.name = name }
}
```

> Structs behave like immutable data in FP — when you pass a struct, you get a copy, not a reference. Swift encourages structs over classes. Think of classes only when you need shared mutable state or inheritance.

**Mutating methods in structs** — structs are immutable by default, must mark mutations explicitly:
```swift
struct Counter {
    var count = 0
    mutating func increment() { count += 1 }
}
```

---

## Enums

Swift enums are algebraic data types — far more powerful than Python enums.

```swift
enum Meal {
    case breakfast, lunch, dinner, snack
}

let current = Meal.breakfast
```

**Associated values** — enums can carry data per case (like tagged unions in FP):
```swift
enum ScanResult {
    case success(calories: Int, protein: Double)
    case failure(error: String)
}

switch result {
case .success(let cal, let prot):
    print("Calories: \(cal)")
case .failure(let err):
    print("Error: \(err)")
}
```

**Raw values** — like Python enums with values:
```swift
enum Priority: Int {
    case low = 1, medium = 2, high = 3
}
Priority.high.rawValue   // 3
```

> Associated value enums map directly to the `Result` / `Either` / `Maybe` pattern from FP. Swift's built-in `Result<Success, Failure>` type is exactly this.

---

## Protocols

Protocols are like Python abstract base classes or interfaces — but more powerful. Also similar to Haskell typeclasses.

```swift
protocol Loggable {
    var id: UUID { get }
    func log() -> String
}

struct FoodEntry: Loggable {
    var id = UUID()
    func log() -> String { "Food logged" }
}
```

**Protocol extensions** — add default implementations, like Python mixins:
```swift
extension Loggable {
    func log() -> String { "Default log: \(id)" }
}
```

**Protocol as type** — use `any` keyword:
```swift
func display(item: any Loggable) { print(item.log()) }
```

> Protocols + extensions are Swift's answer to multiple inheritance. Prefer protocol composition over class inheritance — aligns with FP's "compose over inherit" principle.

---

## Classes & OOP

```swift
class Animal {
    var name: String
    init(name: String) { self.name = name }
    func speak() -> String { "..." }
}

class Dog: Animal {
    override func speak() -> String { "Woof" }
}
```

- `init` = `__init__` in Python
- `override` must be explicit — no accidental overrides
- `super.init()` = `super().__init__()` in Python
- `final` prevents subclassing

---

## SwiftUI Property Wrappers

Property wrappers manage state and data flow in SwiftUI. Understanding which to use is critical.

### `@State`
Local, private mutable state owned by the view. Like a local variable that triggers a re-render when changed.
```swift
@State private var count = 0
```
> Equivalent to `useState` in React or a local instance variable. Use for simple view-local state.

### `@Binding`
A two-way reference to state owned by a parent view. The child can read and write it.
```swift
// Parent passes it down
ChildView(isOn: $isActive)

// Child receives it
struct ChildView: View {
    @Binding var isOn: Bool
}
```
> Like passing a setter function down in FP / React — the child doesn't own the state, just mutates it.

### `@Observable` (iOS 17+ — modern approach)
Marks a class so SwiftUI tracks its changes automatically. Replaces `@ObservableObject`.
```swift
@Observable
class FoodLogViewModel {
    var entries: [FoodEntry] = []
}
```
> Like a reactive store. Views that read its properties automatically re-render on change.

### `@ObservableObject` + `@Published` (pre-iOS 17)
Older pattern — a class that publishes changes.
```swift
class FoodLogViewModel: ObservableObject {
    @Published var entries: [FoodEntry] = []
}

// In view:
@StateObject var vm = FoodLogViewModel()
@ObservedObject var vm: FoodLogViewModel   // when passed in
```

### `@Environment`
Injects shared values from the environment — like dependency injection or a global context.
```swift
@Environment(\.colorScheme) var colorScheme
@Environment(FoodLogViewModel.self) var vm   // injected from parent
```

### `@StateObject`
Owns and creates an `ObservableObject` — use in the view that initializes it.
Use `@ObservedObject` in child views that receive it.

---

## Error Handling

```swift
enum AppError: Error {
    case invalidInput
    case networkFailed(reason: String)
}

func fetchData() throws -> String {
    throw AppError.networkFailed(reason: "timeout")
}

do {
    let data = try fetchData()
} catch AppError.networkFailed(let reason) {
    print("Failed: \(reason)")
} catch {
    print("Unknown error: \(error)")
}
```

> Like Python's `try/except` but typed. Pairs well with `Result<T, Error>` for FP-style error handling without exceptions.

---

## Async / Await

Swift's concurrency model — same concept as Python's `asyncio`.

```swift
func fetchNutrition() async throws -> NutritionData {
    let data = try await api.get("/nutrition")
    return data
}

Task {
    let result = try await fetchNutrition()
}
```

- `async` marks a function as asynchronous
- `await` suspends until the result is ready
- `Task {}` launches async work from a sync context
- `async let` runs tasks in parallel:
```swift
async let a = fetchA()
async let b = fetchB()
let results = try await (a, b)
```
