# Clean Code Adapted for Swift

## Index
  1. [Introduction](#introduction)
  2. [Variables](#variables)
  3. [Functions](#functions)
  4. [Objects and Data Structures](#objects-and-data-structures)
  5. [Classes](#classes)
  6. [SOLID](#solid)
  7. [Testing](#testing)
  8. [Concurrency](#concurrency)
  9. [Error Handling](#error-handling)
  10. [Formatting](#formatting)
  11. [Comments](#comments)

# Introduction

![Humorous image of software quality estimation based on counting how many swear words you shouted while reading the code.](https://github.com/MaatheusGois/clean-code-swift/assets/31082311/33650f3c-7e27-44c1-9dba-4b6b0e359dc3)



Software Engineering principles from Robert C. Martin's book [*Clean Code*](https://github.com/MaatheusGois/clean-code-book/blob/main/The.Robert.C.Martin.Clean.Code.Collection.2011.11.epub), adapted for Swift. This is not a style guide. It is a guide for producing [readable, reusable, and refactorable code](https://github.com/MaatheusGois/3rs-of-software-architecture-for-iOS) in Swift.

It is not necessary to strictly follow all the principles demonstrated, and even less are they a universal consensus. These principles are guidelines and nothing more, however, they have been codified over many years of collective experience by the authors of *Clean Code*.

Software engineering is just over 50 years old, and we are still learning a lot. When software architecture is as old as architecture itself, we may have stricter rules to follow. For now, let these guidelines serve as a criterion for evaluating the quality of Swift code that you and your team produce.

One more thing: learning this will not immediately turn you into a better software developer, and working with these principles for many years does not guarantee that you will not make mistakes. Each portion of code starts as a draft, like wet clay being shaped into its final form. Finally, we carve out imperfections by reviewing with our peers. Don't feel guilty about the first drafts that still need improvement. Instead, focus on refining your code.

# Variables

## Use meaningful and pronounceable variable names

### **Bad:**
```swift
let currentDateStr = DateFormatter.localizedString(from: Date(), dateStyle: .short, timeStyle: .none)
```

**Unclear Variable Name**: The variable name `currentDateStr` suggests it holds a string, which can lead to confusion about its type and usage.

**Inconsistent Naming**: Using "Str" in the variable name introduces inconsistency with other potential date representations, making the code less readable.

**Potential for Mistakes**: If the variable is expected to be a date later on, developers may mistakenly use it as a date type, leading to type errors.

### **Good:**
```swift
let currentDate = DateFormatter.localizedString(from: Date(), dateStyle: .short, timeStyle: .none)
```

**Clear Variable Name**: The variable name `currentDate` indicates that it holds a date, making its purpose clear and improving code readability.

**Type Consistency**: Using a name that reflects the variable's type helps prevent misunderstandings and reduces the chance of errors in type usage.

**Enhanced Readability**: A straightforward name enhances the overall readability of the code, making it easier for others to understand the intent and function of the variable.

**Easier Maintenance**: Clear and consistent naming conventions make the codebase easier to maintain and refactor in the future.


## Use the same vocabulary for the same type of variable

### **Bad:**
```swift
func getUserInfo()
func getClientData()
func getCustomerRecord()
```

### **Good:**
```swift
func getUser()
```


## Use searchable names

We will read more code than we write. It is important that the code we write is readable and searchable. *Not* giving meaningful names to variables that are significant for understanding our program hurts our readers. Make your names searchable.

### **Bad:**
```swift
// What the heck does 86400000 stand for?
setTimeout(blastOff, 86400000)
```

**Unclear Magic Number**: The use of `86400000` directly in the code makes it unclear what this value represents, leading to confusion for anyone reading the code.

**Lack of Context**: Without a descriptive name, the purpose of the number is ambiguous, making the code harder to understand and maintain.

**Reduced Readability**: Using magic numbers reduces overall code readability, as developers cannot easily grasp the meaning behind the value.

### **Good:**
```swift
// Declare them as `let` or `var` in uppercase letters.
let millisecondsPerDay = 86400000

setTimeout(blastOff, millisecondsPerDay)
```

**Descriptive Variable Name**: Defining `millisecondsPerDay` provides clear context, making the purpose of the value immediately understandable.

**Enhanced Clarity**: By using a named constant, the code becomes clearer, improving readability and comprehension for other developers.

**Easier Maintenance**: With a named constant, changes to the value can be made in one place, simplifying future updates and reducing the risk of errors.

**Consistent Coding Practices**: Using `let` or `var` in uppercase letters for constants promotes consistency and helps convey intent within the code.

## Use explanatory variables

### **Bad:**

```swift
let address = "One Infinite Loop, Cupertino 95014"
let cityZipCodeRegex = try! NSRegularExpression(
  pattern: "^[^,\\]+[,\\\\\\s]+(.+?)\\s*(\\d{5})?$"
)

saveCityZipCode(address.match(cityZipCodeRegex)![1], address.match(cityZipCodeRegex)![2])
```

**Unclear Variable Usage**: The use of `address.match(cityZipCodeRegex)!` without explanation makes it unclear what is being extracted.

**Risk of Runtime Errors**: Force unwrapping can lead to crashes if no matches are found, reducing the robustness of the code.

**Reduced Readability**: The code lacks clarity about what is being processed and stored, making it harder to understand.

### **Good:**

```swift
let address = "One Infinite Loop, Cupertino 95014"
let cityZipCodeRegex = try! NSRegularExpression(
  pattern: "^[^,\\]+[,\\\\\\s]+(.+?)\\s*(\\d{5})?$"
)

if let match = cityZipCodeRegex.firstMatch(in: address) {
    let city = (address as NSString).substring(
      with: match.range(at: 1)
    )
    let zipCode = (address as NSString).substring(
      with: match.range(at: 2)
    )

    saveCityZipCode(city, zipCode)
}
```


**Descriptive Variable Names**: Extracting city and zip code into clearly named variables enhances the readability and intent of the code.

**Safe Handling of Matches**: Using optional binding prevents potential crashes, improving the stability of the code.

**Improved Clarity**: The structure of the code makes it clear what each part is doing, facilitating easier maintenance and collaboration.

## Avoid Mental Mapping

Explicit is better than implicit. Using clear and descriptive variable names helps prevent confusion and enhances code readability.

### **Bad:**

```swift
let locations = ["Austin", "New York", "San Francisco"]
locations.forEach({ l in
    doStuff()
    doSomeOtherStuff()
    // ...
    // ...
    // ...
    // Wait, what's `l` for again?
    dispatch(l)
})
```

**Ambiguous Variable Name**: The variable `l` is not descriptive, making it unclear what it represents within the context.

**Cognitive Load**: Readers must mentally map the variable to its intended purpose, leading to confusion and potential errors.

**Reduced Readability**: The code's readability suffers, making it harder for others (or even the original author) to understand the logic quickly.

### **Good:**

```swift
let locations = ["Austin", "New York", "San Francisco"]
locations.forEach({ location in
    doStuff()
    doSomeOtherStuff()
    // ...
    // ...
    // ...
    dispatch(location)
})
```

**Descriptive Variable Name**: Using `location` clarifies the purpose of the variable, enhancing code clarity.

**Lower Cognitive Load**: Clear naming reduces the need for mental mapping, making the code easier to follow.

**Improved Readability**: The explicit nature of the code enhances overall readability, facilitating collaboration and maintenance.

## Avoid Unnecessary Context

Using descriptive names is important, but avoid repeating the object or class name in variable names to keep your code concise and readable.

### **Bad:**
```swift
class User {
    var userName: String
    var userAge: Int
    
    init(userName: String, userAge: Int) {
        self.userName = userName
        self.userAge = userAge
    }
}
```

**Redundant Naming**: The prefixes `userName` and `userAge` add unnecessary context, making the variable names longer without enhancing clarity.

**Decreased Readability**: The repetition of the class name in variable names can clutter the code, making it harder to read.

**Maintenance Challenges**: Changes to the class name would require updating multiple variable names, increasing the risk of inconsistencies.

### **Good:**

```swift
class User {
    var name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}
```

**Concise Variable Names**: Using `name` and `age` eliminates unnecessary context while maintaining clarity about each variable's purpose.

**Enhanced Readability**: The code is cleaner and easier to understand, allowing developers to grasp the logic quickly.

**Simplified Maintenance**: With less redundancy, there’s a lower risk of errors during updates, making the codebase more maintainable.

# Functions

## Function Arguments (Max 2 params)

Careful management of parameters in functions is essential for facilitating testing and maintaining code while adhering to SOLID principles. When a function has more than three arguments, complexity increases significantly, requiring detailed tests for each parameter. It is best to limit function arguments to a maximum of two, and avoid three whenever possible. If more parameters are needed, consider consolidating them into an object. Functions with too many arguments often indicate multiple responsibilities, suggesting a need for reassessment. In many cases, using an object as an argument is a practical solution, especially given Swift's efficiency in creating objects.

### **Bad:**
```swift
func createMenu(title: String, body: String, buttonText: String, cancellable: Bool) {
  // ...
}
```

**Excessive Arguments**: The function has four parameters, which complicates the function signature and increases the cognitive load for users.

**Increased Complexity**: More parameters lead to a higher chance of errors and make testing more difficult, as each parameter needs separate consideration.

**Violation of Single Responsibility**: This function may be trying to handle multiple responsibilities, indicating a design issue that needs to be addressed.

### **Good:**
```swift
struct MenuViewData {
  let title: String
  let body: String
  let buttonText: String
  let cancellable: Bool
}

func createMenu(viewData: MenuViewData) {
  // ...
}

// Usage:
let viewData = MenuViewData(
  title: "Foo", 
  body: "Bar", 
  buttonText: "Baz", 
  cancellable: true
)

createMenu(viewData: viewData)
```

**Consolidated Parameters**: Using a `MenuViewData` struct consolidates multiple parameters into a single object, simplifying the function signature.

**Reduced Complexity**: The function is now easier to use and understand, reducing the cognitive load for developers.

**Adherence to Single Responsibility**: By consolidating parameters, the function focuses on a single responsibility, aligning with best practices and SOLID principles.

## **Use Default Arguments Instead of Short-Circuiting or Using Conditionals**

Default arguments provide cleaner and more readable code compared to short-circuits. Note that default values are only applied to `nullable` arguments; other "falsy" values like `""`, `false`, `null`, and `0` will not trigger default values.

### **Bad:**
```swift
func createMicrobrewery(name: String?) {
    let breweryName = name ?? "Hipster Brew Co."
    // ...
}
```

**Redundant Conditional Logic**: The use of `??` introduces unnecessary complexity when setting a default value for `name`.

**Decreased Readability**: The reliance on conditionals can make the code harder to follow, especially for developers unfamiliar with the logic.

**Potential for Misuse**: If a falsy value is passed, the function will still set `breweryName` to the provided value instead of the default.

### **Good:**
```swift
func createMicrobrewery(breweryName: String = "Hipster Brew Co.") {
    // ...
}
```

**Clean Default Argument**: Using a default argument simplifies the function signature and eliminates the need for additional conditional logic.

**Enhanced Readability**: The code is more straightforward, making it easier for developers to understand the intended behavior of the function.

**Clear Intent**: Default arguments clearly communicate the function's design, ensuring that a sensible default is always available without complicating the implementation.

## Functions Should Do One Thing

This is one of the most important principles in software engineering. Functions that perform multiple tasks become difficult to compose, test, and reason about. By isolating a function to perform just one action, you can refactor more easily, leading to cleaner code. If you remember nothing else from this guide, this principle will put you ahead of many developers.

### **Bad:**
```swift
func emailClients(clients: [Client]) {
    clients.forEach { client in
        let clientRecord = database.lookup(client)
        if clientRecord.isActive() {
            email(client)
        }
    }
}
```

**Multiple Responsibilities**: This function handles both looking up client records and sending emails, violating the single responsibility principle.

**Decreased Testability**: Testing becomes more complex because the function combines logic that could be isolated into separate tests.

**Reduced Readability**: The function's intent is muddied by combining tasks, making it harder to understand at a glance.

### **Good:**
```swift
func emailActiveClients(clients: [Client]) {
    clients
        .filter { isActiveClient(client: $0) }
        .forEach { email(client: $0) }
}

func isActiveClient(client: Client) -> Bool {
    let clientRecord = database.lookup(client)
    return clientRecord.isActive()
}
```

**Single Responsibility**: Each function has a clear, distinct purpose—`emailActiveClients` handles emailing, while `isActiveClient` checks client status.

**Improved Testability**: Smaller, focused functions are easier to test individually, facilitating more effective unit tests.

**Enhanced Readability**: The code is clearer and more straightforward, allowing developers to understand the logic and flow at a glance.


## Function Names Should Say What They Do

Clear and descriptive function names enhance code readability and maintainability. Function names should provide insight into their purpose, making it easier for developers to understand the code at a glance.

### **Bad:**
```swift
func addToDate(date: Date, month: Int) {
    // ...
}

let date = Date()

// It's hard to tell by the function name what is being added
addToDate(date: date, month: 1)
```

**Ambiguous Naming**: The function name `addToDate` does not specify what is being added, leading to confusion about its purpose.

**Decreased Clarity**: Without a clear indication of functionality, developers must delve into the implementation to understand what the function does, hindering readability.

**Potential for Misuse**: The vague name increases the risk of misuse, as developers may not fully grasp the function's intent.

### **Good:**
```swift
func addMonthToDate(month: Int, date: Date) {
    // ...
}

let date = Date()
addMonthToDate(month: 1, date: date)
```

**Descriptive Naming**: The function name `addMonthToDate` clearly indicates that a month is being added to a date, enhancing understanding.

**Improved Clarity**: A straightforward name allows developers to grasp the function's purpose immediately, increasing overall code readability.

**Reduced Risk of Misuse**: Clear naming minimizes the chance of misuse by conveying the function's intent explicitly.


## Functions Should Have One Level of Abstraction

When functions operate on more than one level of abstraction, they tend to become overloaded with responsibilities. Splitting functions into single-level abstractions leads to better reusability and testability.

### **Bad:**

```swift
func parseInput(input: String) {
    // ...
    let inputData = ...
    // ...
    saveData(inputData)
}

func saveData(data: Data) {
    // ...
    database.save(data)
}
```

**Multiple Levels of Abstraction**: The `parseInput` function handles both parsing and saving, leading to a lack of clarity in its responsibilities.

**Reduced Reusability**: Mixing different levels of logic in a single function makes it harder to reuse components in different contexts.

**Complicated Testing**: Functions that combine multiple abstractions are more challenging to test effectively due to their interdependent logic.

### **Good:**

```swift
func parseInput(input: String) {
    // ...
    let inputData = ...
    saveData(data: inputData)
}

func saveData(data: Data) {
    // ...
    database.save(data)
}
```

**Single Level of Abstraction**: Each function focuses on one specific task, improving clarity regarding what each function is responsible for.

**Enhanced Reusability**: With clear separation, both `parseInput` and `saveData` can be reused in other contexts without complications.

**Simplified Testing**: Isolated functions are easier to test individually, allowing for more straightforward unit tests and better code quality.

## Remove Duplicated Code

Do your best to avoid duplicated code. Duplicated code is bad because it means there's more than one place to change something if you need to make a change.

Imagine if you run a restaurant and you keep a list of all your clients in two places: one where you keep the order for the chef and another where you keep the order for the delivery. If you have clients that cancel, now you have to cancel in two places. If you only have one list, there's only one place to update!

What if you forget to update in one place and not the other? What if the delivery guy shows up while the chef is making something and he hasn't seen the cancellation? Now you have a problem.

Often, you have duplicated code because you have two or more slightly different things, that share a lot in common but their differences force you to have two or more separate methods that do much of the same things. Removing duplicated code means creating an abstraction that can handle those differences with only one function/method.

By doing this, you now have only one place to update if something changes.

### **Bad:**
```swift
func showDeveloper(name: String) {
    print("Developer: \(name)")
    print("Coding...")
}

func showManager(name: String) {
    print("Manager: \(name)")
    print("Meeting...")
}
```

**Code Duplication**: The functions `showDeveloper` and `showManager` have similar structures and repeated logic, making them harder to maintain.

**Increased Maintenance Overhead**: Changes to the output format or logic would need to be implemented in multiple places, increasing the chance of errors.

**Potential for Inconsistencies**: If updates are made to one function and not the other, inconsistencies can arise in behavior or output.

### **Good:**
```swift
enum Role {
    case developer
    case manager

    var description: String {
        switch self {
        case .developer: return "Coding..."
        case .manager: return "Meeting..."
        }
    }
}

func showPerson(name: String, role: Role) {
    print("\(role): \(name)")
    print(role.description)
}
```

**Eliminated Duplication**: The `showPerson` function abstracts the common logic while using the `Role` enum to manage specific behavior, reducing code redundancy.

**Simplified Maintenance**: With only one function to update, any changes to the logic or output format can be made in a single location.

**Improved Clarity**: The use of the `Role` enum clearly defines the roles and their specific behaviors, making the code easier to understand and manage.

## Avoid Using Flags as Function Parameters
Functions that have boolean flags as parameters are harder to understand than functions that do only one thing. Flags indicate that the function does more than one thing. Separate these functions into multiple functions if necessary.

### **Bad:**
```swift
func createFile(name: String, temporary: Bool) {
  if temporary {
    // Creates a temporary file
  } else {
    // Creates a permanent file
  }
}
```

### **Good:**
```swift
func createTemporaryFile(name: String) {
  // Creates a temporary file
}

func createPermanentFile(name: String) {
  // Creates a permanent file
}
```


## Avoid Side Effects (Part 1)
A function produces a side effect if it does something other than taking an input value and returning another value(s). A side effect can be writing to a file, modifying a global variable, or accidentally transferring all your money to a stranger.

Now, you need side effects occasionally in your program. Like in the previous example, you might need to write to a file. What you want to do is to centralize where you are doing this. Don't have multiple functions and classes that write to a particular file. Have one service that does it. One and only one.

The main point is to avoid pitfalls like sharing state between objects with no structure, using mutable data types that can be written to by anything, and not centralizing where your side effects occur. If you can do this, you will be much happier than the vast majority of other programmers.

### **Bad:**
```swift
// Global variable referenced by the following function
// If we had another function that uses this name, then it would be an array and could break your code
var name = "Matheus Gois"

func splitIntoFirstAndLastName() {
  name = name.split(separator: " ").joined(separator: " ")
}

splitIntoFirstAndLastName()

print(name) // 'Matheus Gois'
```

### **Good:**
```swift
func splitIntoFirstAndLastName(name: String) -> (firstName: String, lastName: String) {
  let components = name.split(separator: " ").map { String($0) }
  guard components.count >= 2 else {
    return (firstName: name, lastName: "")
  }
  let firstName = components[0]
  let lastName = components[1..<components.count].joined(separator: " ")
  return (firstName: firstName, lastName: lastName)
}

let fullName = "Matheus Gois"
let nameComponents = splitIntoFirstAndLastName(name: fullName)

print(fullName) // 'Matheus Gois'
print(nameComponents.firstName) // 'Ryan'
print(nameComponents.lastName) // 'McDermott'
```


## Avoid Side Effects (Part 2)
In Swift, primitive types are passed by value, and objects/arrays are passed by reference. In the case of objects and arrays, if your function makes a change to a shopping cart array, for example, by adding an item to be purchased, then any other function that uses the `cart` array will also be affected by this addition. This can be great, but it can also be bad. Let's imagine a bad situation:

The user clicks the "Buy" button, which invokes the `purchase` function that sends a series of requests and sends the `cart` array to the server. Due to a poor internet connection, the `purchase` function needs to resend the request. Now, imagine that in the meantime, the user accidentally clicks the `Add to Cart` button on a product he didn't want before the request started. If this happens and the request is sent again, then the `purchase` function will accidentally send the array with the new product added because there is a reference to the `cart` array that the `addItemToCart` function modified by adding an unwanted product.

A great solution would be for the `addItemToCart` function to always clone the `cart` array, edit it, and then return its clone. This ensures that no other function that has a reference to the shopping cart is affected by any changes made.

Two caveats of this approach:

1. There may be cases where you really want to change the input object, but when you adopt this kind of programming, you will find that these cases are quite rare. Most things can be refactored to avoid side effects.
2. Cloning large objects can be quite expensive in terms of performance. Luckily, in practice, this is not a problem because there are [great libraries](https://github.com/apple/swift-collections) that allow this type of programming to be fast and not as memory-intensive as it would be if you manually cloned objects and arrays.

### **Bad:**
```swift
var cart = [
  CartItem(item: "Widget", date: Date()),
  CartItem(item: "Gadget", date: Date())
]

func addItemToCart(item: CartItem) {
  cart.append(item)
}
```

### **Good:**
```swift
func addItemToCart(cart: [CartItem], item: CartItem) -> [CartItem] {
  var updatedCart = cart
  updatedCart.append(item)
  return updatedCart
}
```

## Do Not Write to Global Functions
Polluting globals is a bad practice in Swift because you can conflict with another library, and the user of your API would have no idea until they got an exception thrown in production. Let's think about an example: what if you wanted to extend the native Swift Array method to have a `diff` method that could show the difference between two arrays? You could write your new function on `Array.prototype`, but it could collide with another library that tried to do the same thing. And what if this other library was just using `diff` to find the difference between the first and last element of an array?

### **Bad:**
```swift
extension Array {
  func diff(_ comparisonArray: [Element]) -> [Element] {
    let hash = Set(comparisonArray)
    return filter { !hash.contains($0) }
  }
}
```

### **Good:**
```swift
class ExtendedArray<Element>: Array<Element> {
  func diff(_ comparisonArray: [Element]) -> [Element] {
    let hash = Set(comparisonArray)
    return filter { !hash.contains($0) }
  }
}
```


## Favor Functional Programming over Imperative Programming
Swift is not a functional language in the same way Haskell is, but it has a touch of functional in it. Functional languages are cleaner and easier to test. Favor this type of programming when you can.

### **Bad:**
```swift
let programmerOutput = [
  Programmer(name: "Uncle Bobby", linesOfCode: 500),
  Programmer(name: "Suzie Q", linesOfCode: 1500),
  Programmer(name: "Jimmy Gosling", linesOfCode: 150),
  Programmer(name: "Gracie Hopper", linesOfCode: 1000)
]

var totalOutput = 0

for programmer in programmerOutput {
  totalOutput += programmer.linesOfCode
}
```

### **Good:**
```swift
let programmerOutput

 = [
  Programmer(name: "Uncle Bobby", linesOfCode: 500),
  Programmer(name: "Suzie Q", linesOfCode: 1500),
  Programmer(name: "Jimmy Gosling", linesOfCode: 150),
  Programmer(name: "Gracie Hopper", linesOfCode: 1000)
]

let totalOutput = programmerOutput
  .map { $0.linesOfCode }
  .reduce(0, +)
```


## Encapsulate Conditionals

### **Bad:**
```swift
if fsm.state == "fetching" && isEmpty(listNode) {
  // ...
}
```

### **Good:**
```swift
func shouldShowSpinner(fsm: FSM, listNode: Node) -> Bool {
  return fsm.state == "fetching" && isEmpty(listNode)
}

if shouldShowSpinner(fsm: fsmInstance, listNode: listNodeInstance) {
  // ...
}
```


## Avoid Negations in Conditionals

### **Bad:**
```swift
func isViewNotPresent(view: UIView) -> Bool {
  // ...
}

if !isViewNotPresent(view: view) {
  // ...
}
```

### **Good:**
```swift
func isViewPresent(view: UIView) -> Bool {
  // ...
}

if isViewPresent(view: view) {
  // ...
}
```


## Avoid Conditionals
This seems like an impossible task. The first time people hear this, they say, "How am I supposed to do anything without using `if`?" The answer is that you can use polymorphism to achieve the same task in different cases. The second question is usually, "Well, that's great, but why would I do that?" The answer is a previously learned clean code concept: a function should do only one thing. When you have classes and functions that have `if` statements, you're telling your user that your function does more than one thing. Remember, do one thing.

### **Bad:**
```swift
class Airplane {
  // ...
  func getCruisingAltitude() -> Int {
    switch self.type {
      case "777":
        return self.getMaxAltitude() - self.getPassengerCount()
      case "Air Force One":
        return self.getMaxAltitude()
      case "Cessna":
        return self.getMaxAltitude() - self.getFuelExpenditure()
      default:
        return 0
    }
  }
}
```

### **Good:**
```swift
class Airplane {
  // ...
}

class Boeing777: Airplane {
  // ...
  func getCruisingAltitude() -> Int {
    return self.getMaxAltitude() - self.getPassengerCount()
  }
}

class AirForceOne: Airplane {
  // ...
  func getCruisingAltitude() -> Int {
    return self.getMaxAltitude()
  }
}

class Cessna: Airplane {
  // ...
  func getCruisingAltitude() -> Int {
    return self.getMaxAltitude() - self.getFuelExpenditure()
  }
}
```


## Avoid Type Checking (Part 1)
Swift does not have types, which means your functions can take any type of argument. Sometimes this freedom can bite you, and it becomes tempting to do type checking in your functions. There are many ways to avoid having to do this. The first thing to consider is consistent APIs.

### **Bad:**
```swift
func travelToTexas(vehicle: Any) {
  if let bicycle = vehicle as? Bicycle {
    bicycle.pedal(currentLocation: self.currentLocation, newLocation: Location("texas"))
  } else if let car = vehicle as? Car {
    car.drive(currentLocation: self.currentLocation, newLocation: Location("texas"))
  }
}
```

### **Good:**
```swift
func travelToTexas(vehicle: Vehicle) {
  vehicle.move(currentLocation: self.currentLocation, newLocation: Location("texas"))
}
```


## Avoid Type Checking (Part 2)
If you are working with basic primitive values like strings and integers, and you cannot use polymorphism, but still feel the need to check the type, you should consider using TypeScript. It is an excellent alternative to regular Swift, as it provides static typing on top of Swift's standard syntax. The problem with manual checking in Swift is that to do it well requires so much extra verbosity that the false "type safety" you get doesn't compensate for the loss of readability. Keep your Swift clean, write good tests, and have good code reviews. Or alternatively, do all of that but with TypeScript (which, as I mentioned, is a great alternative!).

### **Bad:**
```swift
func combine(val1: Any, val2: Any) -> String {
  if let number1 = val1 as? Int, let number2 = val2 as? Int {
    return String(number1 + number2)
  } else if let string1 = val1 as? String, let string2 = val2 as? String {
    return string1 + string2
  }

  fatalError("Must be of type String or Number")
}
```

### **Good:**
```swift
func combine(val1: Any, val2: Any) -> String {
  return String(describing: val1) + String(describing: val2)
}
```


## Remove Dead Code
Dead code is as bad as duplicate code. There is no reason to keep it in your codebase. If it's not being called, get rid of it. It will still be safe in your version history if you ever need it.

### **Bad:**
```swift
func oldRequestModule(url: String) {
  // ...
}

func newRequestModule(url: String) {
  // ...
}

let req = newRequestModule
inventoryTracker(item: "apples", requestModule: req, url: "www.inventory-awesome.io")
```

### **Good:**
```swift
func newRequestModule(url: String) {
  // ...
}

let req = newRequestModule
inventoryTracker(item: "apples", requestModule: req, url: "www.inventory-awesome.io")
```


# Objects and Data Structures

## Use Pure Objects

Objects are said to be pure when they don't share state with other objects. Imagine you're in outer space and you have a spaceship. This spaceship has a fuel tank. Imagine there are various different systems in the spaceship that can modify this fuel tank.

There are three different types of objects here:

1. **Impure Object:**
```swift
class Spaceship {
  var fuelTank: Int

  init(fuelTank: Int) {
    self.fuelTank = fuelTank
  }

  func launch() {
    Rocket().ignite(boosters: self.fuelTank)
  }

  func addFuel(fuel: Int) {
    self.fuelTank += fuel
  }
}
```

2. **Less Pure Object:**
```swift
class Spaceship {
  var fuelTank: Int

  init(fuelTank: Int) {
    self.fuelTank = fuelTank
  }

  func launch() {
    Rocket().ignite(boosters: self.fuelTank)
  }

  func visitSpaceStation(spaceStation: SpaceStation) {
    spaceStation.refuel(ship: self)
  }
}

class SpaceStation {
  func refuel(ship: Spaceship) {
    ship.addFuel(fuel: self.fuelTank)
  }

  var fuelTank: Int
}
```

3. **Pure Object:**
```swift
class Spaceship {
  var fuelTank: Int

  init(fuelTank: Int) {
    self.fuelTank = fuelTank
  }

  func launch() {
    Rocket().ignite(boosters: self.fuelTank)
  }

  func refuel(amount: Int) -> Spaceship {
    return Spaceship(fuelTank: self.fuelTank + amount)
  }
}
```

Why are pure objects preferable? They are easier to test and understand. They cannot be changed by other systems while they are being used. Data passed to them can be trusted, and they have no side effects that can cause difficult-to-trace bugs.

## Make decisions based on an object

### **Bad:**
```swift
if car.engine.type == "v8" {
  // ...
}

if bike.tires.type == "fat" {
  // ...


}
```

### **Good:**
```swift
enum Engine {
  case v8
}

enum Tire {
  case fat
}

if car.engine == .v8 {
  // ...
}

if bike.tires == .fat {
  // ...
}
```

## Use Getters and Setters
Using getters and setters to access data in objects is much better than simply looking for a property on an object. "Why?" you might ask. Well, here's an unordered list of reasons:

* When you want to do more than get a property of an object, you don't have to search and change all the accessors in your code.
* Makes it easier to do validation when setting.
* Encapsulates the internal representation.
* Easier to add logging and error handling when getting and setting.
* You can use lazy loading on your object's properties, for example, fetching it from a server.

### **Bad:**
```swift
class User {
    var name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}

// Usage
let user = User(name: "John", age: 30)
print(user.name) // John
user.name = ""   // No validation, name can be empty
user.age = -5    // No validation, age can be negative
print(user.age)  // -5
```


**Lack of Validation**: Properties are set directly without any validation, allowing invalid values (e.g., empty name or negative age).

**Uncontrolled Access**: Public properties can be modified freely, leading to potential inconsistencies and errors.

**No Encapsulation**: Internal data is exposed directly, violating the principle of encapsulation and making the class harder to maintain.

**Inconsistent State Management**: Without validation, the object's state can become inconsistent, leading to potential bugs and unexpected behavior.

**Difficult to Add Logic**: Adding additional logic, such as logging or lazy loading, becomes more difficult without a controlled access mechanism.

### **Good:**
```swift
class User {
    private var _name: String
    private var _age: Int
    
    init(name: String, age: Int) {
        self._name = name
        self._age = age
    }
    
    var name: String {
        get {
            return _name
        }
        set {
            if newValue.isEmpty {
                print("Name cannot be empty.")
            } else {
                _name = newValue
            }
        }
    }
    
    var age: Int {
        get {
            return _age
        }
        set {
            if newValue < 0 {
                print("Age cannot be negative.")
            } else {
                _age = newValue
            }
        }
    }
}

// Usage
let user = User(name: "John", age: 30)
print(user.name) // John
user.name = ""   // No validation, name can be empty
user.age = -5    // No validation, age can be negative
user.age = 25
print(user.age)  // 25
```


**Error Handling**: Getters and setters allow for validation and error handling, ensuring that only valid values are set.

**Encapsulation**: Private variables and controlled access through getters and setters protect the internal state and enforce encapsulation.

**Maintainability**: Encapsulated access makes the code easier to maintain and extend, allowing for additional logic (e.g., logging, validation) to be added in one place.

**Consistency**: Consistent access patterns ensure that data modifications go through the same validation process, preventing inconsistencies.

**Enhanced Readability**: Using getters and setters makes the code more readable and self-explanatory, as the intent and constraints are clearer.



# Classes

## Method Chaining

This pattern is very useful in Swift, and you'll find it in many libraries. It allows your code to be expressive and less verbose. For this reason, I say, use method chaining and see how your code becomes cleaner. In your class functions, just return `self` at the end of each function, and you can chain more class methods on it.

### **Bad:**
```swift
class Car {
    var brand: String
    var speed: Int
    
    init() {
        self.brand = "Generic"
        self.speed = 0
    }
    
    @discardableResult
    func setBrand(_ brand: String) -> Car {
        self.brand = brand
        return self
    }
    
    @discardableResult
    func setSpeed(_ speed: Int) -> Car {
        self.speed = speed
        return self
    }
    
    func printDetails() {
        print("Brand: \(brand), Speed: \(speed)")
    }
}

// Usage
let car = Car()
car.setBrand("Toyota")
    .setSpeed(120)
    .setBrand("")  // No validation, invalid brand
    .setSpeed(-10) // No validation, invalid speed
    .printDetails() // Brand: , Speed: -10
```
## Summary of Errors in the Bad Example

1. **Lack of Validation**: Directly setting properties without validation can lead to invalid states (e.g., empty brand or negative speed).
2. **Uncontrolled Access**: Public properties allow for unrestricted and potentially unsafe modifications.
3. **Inconsistent State Management**: Without proper error handling, invalid values can be set, leading to inconsistencies in the object's state.
4. **Reduced Readability**: Method chaining without error handling can hide potential issues, making the code harder to debug and maintain.


### **Good:**
```swift
class Car {
  private var make: String
  private var model: String
  private var color: String

  init(make: String, model: String, color: String) {
    self.make = make
    self.model = model
    self.color = color
  }

  func setMake(_ make: String) -> Car {
    self.make = make
    return self
  }

  func setModel(_ model: String) -> Car {
    self.model = model
    return self
  }

  func setColor(_ color: String) -> Car {
    self.color = color
    return self
  }

  func save() -> Car {
    print("\(self.make) \(self.model) \(self.color)")
    return self
  }
}

let car = Car(make: "Ford", model: "F-150", color: "red")
car.setColor("pink").save()
```


1. **Error Handling**: Using `Result` allows each step in the chain to handle errors gracefully, improving robustness.
2. **Encapsulation**: Private members and controlled access through methods ensure data integrity and encapsulation.
3. **Fluent Interface**: Method chaining maintains a fluent and readable interface, improving code readability.
4. **Consistency**: Consistent use of methods for state changes ensures that all modifications go through the same validation and error handling process.
5. **Maintainability**: Encapsulated and validated state changes make the code easier to maintain and extend.


## Prefer Composition Over Inheritance
As famously stated in the [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) book by the Gang of Four, you should prefer composition over inheritance where you can. There are many good reasons to use inheritance and many good reasons to use composition. The main point for this maxim is that if your mind instinctively goes for inheritance, try to think if composition could better model your problem. In some cases, it might.

You might be wondering then, "when should I use inheritance?" It depends specifically on your problem, but this is a decent list of when inheritance makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a" relationship (Human→Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing only the base class. (Changing the caloric cost for all animals when they move).

### **Bad:**
```swift
class Employee {
  var name: String
  var email: String

  init(name: String, email: String) {
    self.name = name
    self.email = email
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData: Employee {
  var ssn: String
  var salary: Double

  init(name: String, email: String, ssn: String, salary: Double) {
    self.ssn = ssn
    self.salary = salary
    super.init(name: name, email: email)
  }

  // ...
}
```

### **Good:**
```swift
class EmployeeTaxData {
  var ssn: String
  var salary: Double

  init(ssn: String, salary: Double) {
    self.ssn = ssn
    self.salary = salary
  }

  // ...
}

class Employee {
  var name: String
  var email: String
  var taxData: EmployeeTaxData?

  init(name: String, email: String) {
    self.name = name
    self.email = email
  }

  func setTaxData(ssn: String, salary: Double) {
    self.taxData = EmployeeTaxData(ssn: ssn, salary: salary)
  }
  // ...
}
```


# SOLID

## Single Responsibility Principle (SRP)
As stated in *Clean Code*, "There should never be more than one reason for a class to change." It's tempting to pack a class with many functionalities, like when you can only bring one suitcase on your flight. The problem with this is that your class will not be conceptually cohesive and will give it multiple reasons to change. Minimizing the number of times you need to change a class is important because if many functionalities are in one class and you change a portion of it, it can be difficult to understand how it will affect other modules that depend on it in your code.

### **Bad:**
```swift
class UserSettings {
  var user: User

  init(user: User) {
    self.user = user
  }

  func changeSettings(settings: Settings) {
    if self.verifyCredentials() {
      // ...
    }
  }

  func verifyCredentials() -> Bool {
    // ...
  }
}
```

### **Good:**
```swift
class UserAuth {
  var user: User

  init(user: User) {
    self.user = user
  }

  func verifyCredentials() -> Bool {
    // ...
  }
}

class UserSettings {
  var user: User
  var auth: UserAuth

  init(user: User) {
    self.user = user
    self.auth = UserAuth(user: user)
  }

  func changeSettings(settings: Settings) {
    if self.auth.verifyCredentials() {
      // ...
    }
  }
}
```


## Open/Closed Principle (OCP)
As stated by Bertrand Meyer, "Software entities (classes, modules, functions, etc.) should be open for extension but closed for modification." But what does that mean? This principle basically says that you should allow users to add new functionalities without changing existing code.

### **Bad:**
```swift
class SwiftAdapter: Adapter {
  override init() {
    super.init()
    self.name = "SwiftAdapter"
  }
}

class ObjcAdapter: Adapter {
  override init() {
    super.init()
    self.name = "ObjcAdapter"
  }
}

class HttpRequester {
  var adapter: Adapter

  init(adapter: Adapter) {
    self.adapter = adapter
  }

  func fetch(url: String) -> Promise<Response> {
    if self.adapter.name == "SwiftAdapter" {
      return makeSwiftCall(url: url).then { response in
        // transform the response and return
      }
    } else if self.adapter.name == "httpObjcAdapter" {
      return make

HttpCall(url: url).then { response in
        // transform the response and return
      }
    }
    fatalError("Adapter not supported")
  }
}

func makeSwiftCall(url: String) -> Promise<Response> {
  // make the request and return the promise
}

func makeHttpCall(url: String) -> Promise<Response> {
  // make the request and return the promise
}
```

### **Good:**
```swift
class SwiftAdapter: Adapter {
  override init() {
    super.init()
    self.name = "SwiftAdapter"
  }

  func request(url: String) -> Promise<Response> {
    // make the request and return the promise
  }
}

class ObjcAdapter: Adapter {
  override init() {
    super.init()
    self.name = "ObjcAdapter"
  }

  func request(url: String) -> Promise<Response> {
    // make the request and return the promise
  }
}

class HttpRequester {
  var adapter: Adapter

  init(adapter: Adapter) {
    self.adapter = adapter
  }

  func fetch(url: String) -> Promise<Response> {
    return self.adapter.request(url: url).then { response in
      // transform the response and return
    }
  }
}
```


## Liskov Substitution Principle (LSP)
This is a scary term for a very simple concept. It's formally defined as "If S is a subtype of T, then objects of type T may be replaced with objects of type S (i.e., objects of type S may substitute objects of type T) without altering any of the desirable properties of a program (correctness, task performance, etc.)." That's an even scarier definition.

The best explanation for this concept is if you have a parent class and a child class, then the base class and the child class can be used interchangeably without getting incorrect results. This might still be confusing, so let's look at the classic example of the Square-Rectangle problem. Mathematically, a square is a rectangle, but if you model it using an "is-a" relationship through inheritance, you quickly run into problems.

### **Bad:**
```swift
class Rectangle {
  var width: Double
  var height: Double

  init() {
    self.width = 0
    self.height = 0
  }

  func setColor(color: String) {
    // ...
  }

  func render(area: Double) {
    // ...
  }

  func setWidth(width: Double) {
    self.width = width
  }

  func setHeight(height: Double) {
    self.height = height
  }

  func getArea() -> Double {
    return self.width * self.height
  }
}

class Square: Rectangle {
  override func setWidth(width: Double) {
    self.width = width
    self.height = width
  }

  override func setHeight(height: Double) {
    self.width = height
    self.height = height
  }
}

func renderLargeRectangles(rectangles: [Rectangle]) {
  rectangles.forEach { rectangle in
    rectangle.setWidth(width: 4)
    rectangle.setHeight(height: 5)
    let area = rectangle.getArea() // BAD: Returns 25 for the Square. Should be 20.
    rectangle.render(area: area)
  }
}

let rectangles: [Rectangle] = [Rectangle(), Rectangle(), Square()]
renderLargeRectangles(rectangles: rectangles)
```

### **Good:**
```swift
class Shape {
  func setColor(color: String) {
    // ...
  }

  func render(area: Double) {
    // ...
  }
}

class Rectangle: Shape {
  var width: Double
  var height: Double

  init(width: Double, height: Double) {
    self.width = width
    self.height = height
  }

  func getArea() -> Double {
    return self.width * self.height
  }
}

class Square: Shape {
  var length: Double

  init(length: Double) {
    self.length = length
  }

  func getArea() -> Double {
    return self.length * self.length
  }
}

func renderLargeShapes(shapes: [Shape]) {
  shapes.forEach { shape in
    let area = shape.getArea()
    shape.render(area: area)
  }
}

let shapes: [Shape] = [Rectangle(width: 4, height: 5), Rectangle(width: 4, height: 5), Square(length: 5)]
renderLargeShapes(shapes: shapes)
```


## Interface Segregation Principle (ISP) with Protocols
Although Swift doesn't adopt the traditional concept of interfaces, we can apply the Interface Segregation Principle (ISP) through protocols, leveraging Swift's flexibility.

ISP states that "Clients should not be forced to depend on interfaces they do not use." In Swift, where duck typing prevails, we can create protocols that represent segregated interfaces.

### **Bad:**
```swift
// Single protocol with many requirements
protocol Styling {
    var font: UIFont { get }
    var backgroundColor: UIColor { get }
    var cornerRadius: CGFloat { get }

    func applyStyles()
}

// Protocol implementation
class BadStylableView: Styling {
    var font: UIFont
    var backgroundColor: UIColor
    var cornerRadius: CGFloat

    init(font: UIFont, backgroundColor: UIColor, cornerRadius: CGFloat) {
        self.font = font
        self.backgroundColor = backgroundColor
        self.cornerRadius = cornerRadius
    }

    func applyStyles() {
        // Apply styles based on configuration
    }
}

// Example of usage
let badView = BadStylableView(font: .systemFont(ofSize: 14), backgroundColor: .white, cornerRadius: 8)
```

### **Good:**
```swift
// Protocol for styling configuration
protocol StyleConfigurable {
    var font: UIFont { get }
    var backgroundColor: UIColor { get }
    var cornerRadius: CGFloat { get }
}

// Protocol for applying styles
protocol StyleApplicable {
    func applyStyles()
}

// Default implementation for styling configuration
struct AppearanceConfig: StyleConfigurable {
    var font: UIFont
    var backgroundColor: UIColor
    var cornerRadius: CGFloat
}

// View adopting the protocols
class GoodStylableView: StyleApplicable {
    var styleConfig: StyleConfigurable

    init(styleConfig: StyleConfigurable) {
        self.styleConfig = styleConfig
        self.setup()
    }

    func setup() {
        applyStyles()
    }

    func applyStyles() {
        // Apply styles based on configuration
    }
}

// Example of usage
let goodView = GoodStylableView(styleConfig: AppearanceConfig(font: .systemFont(ofSize: 14)))
```

In the bad example, a single `Styling` protocol contains many requirements, forcing clients to implement methods and properties that may not be necessary. In the good example, we use segregated protocols (`StyleConfigurable` and `StyleApplicable`) to allow for a more specific and flexible implementation.


## Dependency Inversion Principle (DIP)
This principle tells us two essential things:

1. High-level modules should not depend on low-level modules. Both should depend on abstractions.
2. Abstractions should not depend on details. Details should depend on abstractions.

This might be

 hard to understand at first, but if you've worked with AngularSwift, you've seen an implementation of this principle in the form of Dependency Injection (DI). While they are not identical concepts, DIP does not allow high-level modules to know the details of their low-level modules but configures them. This can be achieved through DI. A significant benefit is that it reduces coupling between modules. Coupling is a very bad development pattern because it makes your code harder to refactor.

As mentioned earlier, Swift doesn't have interfaces, so the abstractions needed are implicit contracts. That means the methods and classes that an object/class exposes to other objects/classes. In the example below, the implicit contract is that any Request module for `InventoryTracker` will have a `requestItems` method:

### **Bad:**
```swift
class InventoryRequester {
  init() {
    self.REQ_METHODS = ["HTTP"]
  }

  func requestItem(item: String) {
    // ...
  }
}

class InventoryTracker {
  var items: [String]
  var requester: InventoryRequester

  init(items: [String]) {
    self.items = items

    // Bad: We created a dependency on a specific request implementation.
    // We should only have requestItems depend on a request method: `request`
    self.requester = InventoryRequester()
  }

  func requestItems() {
    self.items.forEach { item in
      self.requester.requestItem(item: item)
    }
  }
}

let inventoryTracker = InventoryTracker(items: ["apples", "bananas"])
inventoryTracker.requestItems()
```

### **Good:**
```swift
class InventoryTracker {
  var items: [String]
  var requester: RequesterProtocol

  init(items: [String], requester: RequesterProtocol) {
    self.items = items
    self.requester = requester
  }

  func requestItems() {
    self.items.forEach { item in
      self.requester.requestItem(item: item)
    }
  }
}

protocol RequesterProtocol {
  func requestItem(item: String)
}

class InventoryRequesterV1: RequesterProtocol {
  func requestItem(item: String) {
    // ...
  }
}

class InventoryRequesterV2: RequesterProtocol {
  func requestItem(item: String) {
    // ...
  }
}

// Building our dependencies externally and injecting them, we can easily
// swap our request module for a new fancy one that uses WebSockets
let inventoryTracker = InventoryTracker(items: ["apples", "bananas"], requester: InventoryRequesterV2())
inventoryTracker.requestItems()
```


# Testing
Tests are more critical than shipping. If you have no tests or an inadequate amount, then every time you ship code, you won't be sure if you didn't break anything. Deciding what constitutes an adequate amount is up to your team, but having 100% coverage (all statements and branches) is how you achieve very high confidence and peace of mind. This means that in addition to having a great testing framework, you also need to use a [good coverage tool](https://www.sonarsource.com/products/sonarqube/).

There's no excuse for not writing tests. There are [many great testing frameworks for Swift](https://github.com/vsouza/awesome-ios#testing), so find one that your team prefers. When you find one that works for your team, then aim to always write tests for every new feature/module you introduce. If your preferred method is Test-Driven Development (TDD), that's great, but the main point is to ensure you are reaching your coverage goals before launching any feature or refactoring an existing one.

## One Concept per Test

### **Bad:**
```swift
import XCTest

class MakeMomentSwiftGreatAgainTests: XCTestCase {

  func testHandlesDateBoundaries() {
    var date = MakeMomentSwiftGreatAgain("1/1/2015")
    date.addDays(30)
    XCTAssertEqual("1/31/2015", date)

    date = MakeMomentSwiftGreatAgain("2/1/2016")
    date.addDays(28)
    XCTAssertEqual("02/29/2016", date)

    date = MakeMomentSwiftGreatAgain("2/1/2015")
    date.addDays(28)
    XCTAssertEqual("03/01/2015", date)
  }
}
```

### **Good:**
```swift
import XCTest

class MakeMomentSwiftGreatAgainTests: XCTestCase {

  func testHandles30DayMonths() {
    let date = MakeMomentSwiftGreatAgain("1/1/2015")
    date.addDays(30)
    XCTAssertEqual("1/31/2015", date)
  }

  func testHandlesLeapYear() {
    let date = MakeMomentSwiftGreatAgain("2/1/2016")
    date.addDays(28)
    XCTAssertEqual("02/29/2016", date)
  }

  func testHandlesNonLeapYear() {
    let date = MakeMomentSwiftGreatAgain("2/1/2015")
    date.addDays(28)
    XCTAssertEqual("03/01/2015", date)
  }
}
```


# Concurrency
## Async/Await is even cleaner than Promises
After iOS 13, Swift introduces `async` and `await` that offer an even cleaner solution. All you need is a function prefixed with the `async` keyword, and then you can write your logic imperatively without using `completions` to chain your functions. Use this if you can take advantage of Swift's features today!

### **Bad:**
```swift
import Foundation

let url = URL(string: "https://en.wikipedia.org/wiki/Robert_Cecil_Martin")!

URLSession.shared.dataTask(with: url) { (data, response, error) in
    guard let data = data, error == nil else {
        print(error?.localizedDescription ?? "Unknown error")
        return
    }

    do {
        try data.write(to: URL(fileURLWithPath: "article.html"))
        print("File written")
    } catch {
        print(error.localizedDescription)
    }
}.resume()
```

### **Good:**
```swift
import Foundation

async func getCleanCodeArticle() {
    let url = URL(string: "https://en.wikipedia.org/wiki/Robert_Cecil_Martin")!

    do {
        let (data, _) = try await URLSession.shared.data(from: url)
        try await data.write(to: URL(fileURLWithPath: "article.html"))
        print("File written")
    } catch {
        print(error.localizedDescription)
    }
}

Task {
    await getCleanCodeArticle()
}
```



# Error Handling
`throw error` is a good thing! They mean the program has successfully identified when something went wrong and is letting you know by halting the execution of the current function, unwinding the process (in Swift), and notifying you in the console with the process stack.

## Don't ignore caught errors
Doing nothing with a caught error doesn't give you the ability to address it or react to the reported error. Just printing a log to the console (`print`) is not much better because it can often get lost among a bunch of other things printed to the console. If you wrap any piece of code in a `do/catch`, it means you believe an error might occur there and then you should have a plan, or create a code path for when it happens.

### **Bad:**
```swift
do {
    try funcThatMightThrow()
} catch {
    print(error)
}
```

### **Good:**
```swift
do {
    try funcThatMightThrow()
} catch {
    // One option (more noticeable than print):
    print(error)
    // Another option:
    notifyUserOfError(error)
    // Another option:
    reportErrorToService(error)
    // OR all three!
}
```

# Formatting

Formatting is subjective. Like many of the rules here, there's no hard and fast rule you need to follow. The main idea is NOT to argue about formatting. There are tools that automate this process; let them handle it. Most Swift code should follow the [Google Swift Style Guide](https://google.github.io/swift/).

## Use spaces instead of tabs
Despite the epic struggle between spaces and tabs, the important thing is to be consistent. Swift uses spaces, and it's a common practice in other Swift projects. Do the same.

### **Bad:**
```swift
func bad() {
∙∙∙∙var name:String?
∙∙∙∙guard let unwrappedName = name else {
∙∙∙∙∙∙∙return
∙∙∙∙}
}
```

### **Good:**
```swift
func good() {
    var name: String?
    guard let unwrappedName = name else {
        return
    }
}
```

## Use blank lines effectively
Separating code blocks with blank lines can make the code more readable and organized. However, excessive blank lines can have the opposite effect, creating a sense of fragmentation. Use blank lines sparingly.

### **Bad:**
```swift
func calculateTotalScore(score: Int) {
    
    var totalScore = 0
    
    for i in 1...score {
        
        totalScore += i
        
    }
    
    print("The total score is: \(totalScore)")
    
}
```

### **Good:**
```swift
func calculateTotalScore(score: Int) {
    var totalScore = 0
    
    for i in 1...score {
        totalScore += i
    }
    
    print("The total score is: \(totalScore)")
}
```

## Limit line length
The recommended length for a code line is 80 characters. This ensures that you don't have to scroll horizontally to read the code. It's common in many development environments to have two files side by side. Making this possible makes reading the code easier.

### **Bad:**
```swift
let errorMessage = "This is a very long error message that exceeds the recommended line length of 80 characters and makes the code harder to read."
```

### **Good:**
```swift
let errorMessage = "This is a short error message."
```

## Use consistent spacing
Maintaining consistency in the use of spaces around operators and after commas contributes to code readability.

### **Bad:**
```swift
let sum = 1+2
let array = [1 , 2,3, 4]
```

### **Good:**
```swift
let sum = 1 + 2
let array = [1, 2, 3, 4]
```

## Avoid excessive whitespace
Excessive whitespace can visually clutter the code and make it less readable. Maintain a moderate use of whitespace.

### **Bad:**
```swift
func   foo  (   a : Int  , b   :   Int   ) -> Int {
    return a + b
}
```

### **Good:**
```swift
func foo(a: Int, b: Int) -> Int {
    return a + b
}
```

## Follow the formatting recommended by SwiftLint
Code formatting can be a controversial topic, but it's important to maintain a consistent standard within your project. SwiftLint is a helpful tool for enforcing formatting standards. Integrate SwiftLint into your workflow to ensure the code follows recommended practices.


# Comments

## Only comment on things that have business logic complexity.
Comments should be used to explain parts of the code that have non-obvious complexity or to provide additional information about business logic. Avoid commenting on the obvious or trivial details that can be easily understood by reading the code.

### **Bad:**
```swift
func calculateTotalScore(score: Int) {
  // Initialize the total score
  var totalScore = 0
  
  // Loop through each individual score
  for i in 1...score {
    // Add the individual score to the total score
    totalScore += i
  }
  
  // Print the total score
  print("The total score is: \(totalScore)")
}
```

### **Good:**
```swift
func calculateTotalScore(score: Int) {
    // The total score is calculated using the Gauss sum formula
    var totalScore = (score * (score + 1)) / 2
    
    // Print the total score
    print("The total score

 is: \(totalScore)")
}
```


## Don't leave commented-out code in your codebase
Version control (like Git) is a powerful tool for tracking changes over time. There's no need to keep commented-out code in the codebase as it only adds noise and makes it harder to read.

### **Bad:**
```swift
func doSomething() {
    // Previous code that is no longer needed
    // ...
    
    // Code commented out for future reference
    /*
    if someCondition {
        // Code to be executed
    }
    */
    
    // ...
}
```

### **Good:**
```swift
func doSomething() {
    // Current code
    // ...
}
```


## Avoid change markers
Avoid using change markers, such as slashes or lines of asterisks, to divide or highlight sections of code. Instead, use good code structure with proper indentation and formatting to make the code easily understandable.

### **Bad:**
```swift
struct Example {
    // MARK: - Properties
    var name: String
    var age: Int
    
    // MARK - Initializer
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    // MARK - Functions
    
    // Method to perform an action
    func performAction() {
        // ...
    }
}
```

### **Good:**
```swift
struct Example {
    var name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    func performAction() {
        // ...
    }
}
```

