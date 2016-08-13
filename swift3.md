# swift3 官方beta [2016-08-13](https://swift.org/documentation/#the-swift-programming-language)
# 欢迎来到swift世界
## swift快速入门
通常我们学习第一门语言的时候都会来个‘hello world’。在swift中只需
```swift
print("hello world")
```
如果你写过C或者OC的话，会觉得他们看起来很像。在swift中不需要导入额外的库，如input/output或者string处理函数。这行代码就是程序的入口，swift不需要main()函数，（类似于python）也不需要在末尾添加“;”。  
这个教程会通过大量编程实例，来告诉你关于swift编程的知识。如果不明白的话，不要紧，本书的后面会有非常详细的介绍。  
### 简单值
通过`let`定义常量，`var`定义变量。在编译时不需要知道常量的值，但是你必须在定义时就给他赋值。
```swift
var myVariable = 42
myVariable = 50
let myConstant = 42
```
给变量赋值时，值得类型，必须与变量类型一致。然而你并不需要在定义变量是都制定类型，swift会自动判断值的类型，并推导出变量的类型。例如：`myVariable`是整型，因42就是整型。  
如果值不能确定类型的话，你可以定义变量的类型
```swift
let explicitDouble: Double = 70
```
数值不会被自动的转换为其他类型。如果你需要将一个数值转换为其他类型，你需要生成该类型的对象。
```swift
let label = "The width is"
let width = 94
let widthLabel = label + String(width)
```
swift提供了非常简便的方法来将变量包含到字符串中。你只需要使用`\(变量名)`。
```swift
let apples = 3
let oranges = 5
let appleSummary = "I have \\(apples) apples."
let fruitSummary = "I have \\(apples + oranges) pieces of fruit.”
```
使用`[]`来创建列表和字典，通过下标`index`或者键`key`，访问他们的元素。最后一个元素后面允许跟着一个逗号`,`。
```swift
var shoppingList = ["catfish", "water", "tulips", "blue paint"]
shoppingList[1] = "bottle of water"
var occupations = ["Malcolm": "Captain","Kaylee": "Mechanic",]
occupations["Jayne"] = "Public Relations”
```
创建空的列表或者字典，只需要使用初始化语句即可
```swift
let emptyArray = [String]()
let emptyDictionary = [String:Float]()
```
如果不确定类型的话,可以直接使用`[]`创建空列表,`[:]`创建空字典。例如:**你给一个变量赋值或者给一个函数传递参数**。
```swift
shoppingList = []
occupations = [:]
```
### 控制流
使用`if`和`switch`来创造条件,使用`for-in`,`for`,`while`,`repeat-while`,来创建循环。条件两边的`()`是可选的,`{}`是必选的。
```swift
let individualScores = [75, 43, 103, 87, 12]  
var teamScore = 0
for score in individualScores {
    if score > 50 {
        teamScore += 3
    }else {
        teamScore +=1
    }
}
print(teamScore)
```
在`if`语句中,条件必须是Boolean类型,如果使用`if score {}`会报错,swift不会隐式转换数据的类型。  
你可以使用`if`和`let`一起来处理可能没有值的变量。这些值被当做`optional`,optional类型会在后面详解。optional类型要么包含一个值,要么包含`nil`,`nil`表示没有值。在变量类型后面加上`?`将变量变为optional类型。
```swift
var optionalString: String? = "Hello"
print(optionalString == nil)
var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
}
```
如果optional值是`nil`,那么`if`语句的条件将会是`false`。否将会取出optional的值并赋给变量。  
另外一种处理optional值的方式是通过`??`给一个默认值,如果optional没有值的话,将会使用默认值。取出optional的值使用`!`
```swift
let nickName: String? = nil
let fullName: String? = "John Appleseed"
let informalGreeting = "HI \(nickName ?? fullName)"
print(informalGreeting) //HI Optional("John Appleseed")
print(fullName)//Optional("John Appleseed")
print(fullName!)//"John Appleseed"
```
**switch** 支持任意类型的数据和以及多种比较方式。
```swift
let vegetable = "red pepper"
switch vegetable {
    case "celery":
        print("Add some raisins and make ants on a log.")
    case "cucumber","watercress":
        print("That would make a good tea sandwich")
    case let x where x.hasSuffix("pepper")
        print("Is it a spicy \(x)?")
    default:
        print("EverEverything tastes good in soup")
}
```
注意`let`可以用来做模式匹配。每一个case匹配之后都会自动break。
**for-in** 可以通过(key,value)循环一个字典中的元素,字典是无序的(类似于python无序字典),所以他的键值对也是任意顺序。他同样可循环访问一个列表的中的元素。
```swift
let interestingNumbers = [
    "Prime":[2, 3, 4, 5, 6, 7],
    "Fibonacci":[1, 1, 2, 3, 5, 8],
    "Square":[1, 4, 9, 16, 25],
]
var largest = 0
for (kind, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest{
            largest = number
        }
    }
    print(kind,largest)
}
//Prime 7
//Fibonacci 8
//Square 25
```
**while** 可以在条件范围内,循环执行代码。条件可以再代码开头定义,也可以在代码结尾定义。
```swift
var n = 2
while n < 10 {
    n = n * 2
    print(n)
}
var m = 2
repeat {
    m = m * 2
    print(m)
}while m < 10
```
可以通过`..<`来构建一个iterator,类似于python的range()。  
- 0..<4 表示不包含0,1,2,3
- 0...4表示包含0,1,2,3,4
```swift
var total = 0
for i in 0..<4 {
    total += i
}
print(total)//6

total = 0
for i in 0...4 {
    total += i
}
print(total)//10
```
### 函数和闭包
`func`定义一个函数,`->`跟着返回值类型
```swift
func greet(person: String, day: String) -> String{
    return "hello \(person), today is \(day)"
}
greet(person: "Bob", day: "Tuesday")
```
默认情况下,函数使用它们参数的名称作为label,在参数名称前添加label,或者使用`_`表示不使用label。
```swift
func greet(_ person: String, on day: String) -> String{
    return "hello \(person), today is \(day)"
}
greet("Bob", on: "Tuesday")
```
将元祖类型作为返回值类型,可以一次返回多个值,元祖可以通过名称或者元素的位置数值访问其中包含的元素。(tuple.itemname,tuple.1)
```swift
func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
    var min = scores[0]
    var max = scores[0]
    var sum = 0

    for score in scores {
        if score > max {
            max = score
        } else if score < min {
            min = score
        }
        sum += score
    }

    return (min, max, sum)
}
let statistics = calculateStatistics(scores: [5, 3, 100, 3, 9])
print(statistics.sum)
print(statistics.2)
```
函数还可以不同数量的参数,将他们收进一个列表中
```swift
func sumOf(numbers: Int...) -> Int {
    var sum = 0
    for number in numbers {
        sum += number
    }
    return sum
}
sumOf()
sumOf(numbers: 42, 597, 12)
```
函数还可作为内置函数,他可以访问外部函数的变量。
```swift
func returnFifteen() -> Int {
    var y = 10
    func add() {
        y += 5
    }
    add()
    return y
}
returnFifteen()
```
函数也是一种类型,也就是函数可以返回一个函数类型。
```swift
func makeIncrementer() -> ((Int) -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }
    return addOne
}
var increment = makeIncrementer()
increment(7)
```
函数可以将其他函数作为参数
```swift
func hasAnyMatches(list: [Int], condition: (Int) -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    return number < 10
}
var numbers = [20, 19, 7, 12]
hasAnyMatches(list: numbers, condition: lessThanTen)
```
