# swift3 官方beta [2016-08-13](https://swift.org/documentation/#the-swift-programming-language)
反馈:`canhuayin@gmail.com`
> 注意:2.0和3.0语法上有很大差别

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
函数实际是一种图书的闭包,他可以在定义之后被其他代码调用。可以用`{}`来构建匿名闭包。使用`in`将函数定义域函数体分离
```swift
numbers.map({
    (number: Int) -> Int in
    let result = 3 * number
    return result
})
```
有很多种方法可以定义更加间接地闭包,例如:如果你只到函数类型(参数类型,返回值),那么可以省略掉参数的类型,以及返回类型
```swift
let mappedNumbers = numbers.map({ number in 3 * number })
print(mappedNumbers)
```
你可以通过参数位置而不是参数名字来引用参数——这个方法在非常短的闭包中非常有用,当一个闭包作为最后一个参数传给一个函数的时候,它可以直接跟在括号后面。当一个闭包是传给函数的唯一参数,你可以完全忽略
括号。
### 对象和类
通过`class`定义一个类,类的属性以及函数定义方式与普通变量以及函数定义一样,只不过他们属于类。
```swift
class Shape {
    var numberOfSides = 0
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```
创建类的实例以及访问属性和方法
```swift
var shape = Shape()
shape.numberOfSides = 7
var shapeDescription = shape.simpleDescription()
```
类的构造函数为`init`,self代表实例本身,类中的任何一个属性都必须赋值,如果定义时没有给出值,那么在init中必须给其赋值。
```swift
class NamedShape {
    var numberOfSides: Int = 0
    var name: String
    init(name: String) {
        self.name = name
    }
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```
如果你需要在对象实例被删除之前做一些操作,那么可以将这些操作写在`deinit`函数中
子类想要重写父类方法,必须使用`override`,没有override会报错。子类调用父类方法通过`super`对象
```swift
class Square: NamedShape {
    var sideLength: Double
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 4
}
    func area() ->  Double {
        return sideLength * sideLength
}
    override func simpleDescription() -> String {
        return "A square with sides of length \(sideLength)."
} }
let test = Square(sideLength: 5.2, name: "my test square")
test.area()
test.simpleDescription()
```
类的属性还可以拥有`set`和`get`方法
```swift
class EquilateralTriangle: NamedShape {
    var sideLength: Double = 0.0
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 3
    }
    var perimeter: Double {
        get {
            return 3.0 * sideLength
        }
        set {
            sideLength = newValue / 3.0
        }
    }
    override func simpleDescription() -> String {
        return "An equilateral triagle with sides of length \(sideLength)."
    }
}
var triangle = EquilateralTriangle(sideLength: 3.1, name: "a triangle")
print(triangle.perimeter)
```
在`set`中默认的参数名为`newValue`,也可以自定义一个参数名,`set(newVal){}`,不一定一定要有set,如果没有set的话变量是只读的。  
**注意** `EquilateralTriangle`的构造函数执行了三步:
- 设置sideLength
- 调用父类构造函数
- 修改父类numberOfSides的值

变量观察者(有点意思),变量赋值有两个会触发两个函数`willSet`,`didSet`,变量必须是`var`,默认情况下`willSet`接受`newValue`参数,也可以自定义一个名字`willSet(newVal)`,`didSet`接受一个'oldValue'参数,也可以自定义`didSet(oldVal)`,如果变量类型是以引用类型,例如class,那么该实例的属性值变化并不会出发willSet,因为变量的保存的时实例的地址,只要地址不变,就不会触发。(后面会讲到引用类型和数值类型)
```swift
class TriangleAndSquare {
    var triangle: EquilateralTriangle {
        willSet {
            square.sideLength = newValue.sideLength
        }
    }
    var square: Square {
        willSet {
            triangle.sideLength = newValue.sideLength
        }
    }
    init(size: Double, name: String) {
        square = Square(sideLength: size, name: name)
        triangle = EquilateralTriangle(sideLength: size, name: name)
    }
}
var triangleAndSquare = TriangleAndSquare(size: 10, name: "another test shape")
print(triangleAndSquare.square.sideLength)
print(triangleAndSquare.triangle.sideLength)
triangleAndSquare.square = Square(sideLength: 50, name: "larger square")
print(triangleAndSquare.triangle.sideLength)
```
optional类型在变量定义时加`?`,如果变量的值为nil那么`?`之后代码不会运行
```swift
let optionalSquare: Square? = Square(sideLength: 2.5, name: "optional square")
let sideLength = optionalSquare?.sideLength
```
### 枚举和机构体
使用`enum`来创建一个枚举,枚举也可以有自己的函数,也有构造函数,enum类似于一个已知状态的集合,集合中任意一个元素独立
```swift
enum Rank: Int {
    case Ace = 1
    case Two, Three, Four, Five, Six, Seven, Eight, Nine, Ten
    case Jack, Queen, King
    func simpleDescription() -> String {
        switch self {
        case .Ace:
            return "ace"
        case .Jack:
            return "jack"
        case .Queen:
            return "queen"
        case .King:
            return "king"
        default:
            return String(self.rawValue)
        }
    }
}
let ace = Rank.Ace
let aceRawValue = ace.rawValue
```
枚举元素的原始值默认类型是Int,并且从0开始递增。可以通过`.rawValue`获取原始值。上面我们指定Ace=1,那么初始值会从1开始递增。也可以自定义原始值,并且原始值类型可以是字符串或者小数。  
使用`init?(rawValue:)`创建enum实例,
```swift
if let convertedRank = Rank(rawValue: 3) {
    let threeDescription = convertedRank.simpleDescription()
}
```
枚举的元素值是实际的值,并不是另一种书写方式。通常为了不让原始值没有意义,你不需要自己提供原始值。
```swift
enum Suit {
    case Spades, Hearts, Diamonds, Clubs
    func simpleDescription() -> String {
        switch self {
        case .Spades:
            return "spades"
        case .Hearts:
            return "hearts"
        case .Diamonds:
            return "diamonds"
        case .Clubs:
            return "clubs"
        }
    }
}
let hearts = Suit.Hearts
let heartsDescription = hearts.simpleDescription()
```
**注意** 这里我们定义了常量`let hearts = Suit.Hearts`,他必须通过`Suit.Hearts`。然而在枚举内部的switch中,我们并没有使用`Suit.*`,而是直接使用`.*`,这是合法的,因为我们提前知道`self`就`Suit`。(后面会详细讲解enum的特性,自定义值,取值..)  
使用`struct`来定义结构体,结构体有自己的属性和方法,类似于enum以及class,但是`struct`和'enum'都是值类型,而`class`是引用类型。值类型和引用类型的区别:值类型总是赋值一份新的,引用类型却只是将地址传递过来。值类型互补干扰,而引用类型,任何一个地方改变,会影响到全部引用变量。
```swift
struct Card {
    var rank: Rank
    var suit: Suit
    func simpleDescription() -> String {
        return "The \(rank.simpleDescription()) of \(suit.simpleDescription())"
    }
}
let threeOfSpades = Card(rank: .Three, suit: .Spades)
let threeOfSpadesDescription = threeOfSpades.simpleDescription()
```
### 协议和扩展
protocol是一个对象类型,但是并没存在protocol对象,我们无法实例化他。protocol只是列出一些属性和方法,属性没有值.实际的对象类型将会完整的定义他们。枚举,结构体,类都可以*实现*协议。
```swift
protocol ExampleProtocol {
    var simpleDescription: String { get }
    mutating func adjust()
}
class SimpleClass: ExampleProtocol {
    var simpleDescription: String = "A very simple class."
    var anotherProperty: Int = 69105
    func adjust() {
        simpleDescription += "  Now 100% adjusted."
    }
}
var a = SimpleClass()
a.adjust()
let aDescription = a.simpleDescription
struct SimpleStructure: ExampleProtocol {
    var simpleDescription: String = "A simple structure"
    mutating func adjust() {
        simpleDescription += " (adjusted)"
    }
}
var b = SimpleStructure()
b.adjust()
let bDescription = b.simpleDescription
```
**注意** 在定义protocol是我们使用了`mutating`这个关键字用于`struct`中,表示这个方法会改变属性的值。(后面会详细介绍)
`extension`用于扩展一个类型,例如添加属性,方法。
```swift
extension Int: ExampleProtocol {
    var simpleDescription: String {
        return "The number \(self)"
    }
    mutating func adjust() {
self += 42 }
}
print(7.simpleDescription)
```
### 错误处理
swift中有一`Error`协议,我们可以*adopt*他来自定义错误类型。
```swift
enum PrintError:Error{
    case outOfPaper
    case noToner
    case onFire
}
```
使用`throw`来触发错误,在函数后面跟上`throws`表示这个函数可以抛出错误
```swift
func send(job: Int, toPrinter printerName: String) throws -> String {
    if printerName == "Never Has Toner" {
        throw PrinterError.noToner
    }
    return "Job sent"
}
```
有很多方式来处理异常  
第一种'do-catch',在`do`代码块中,在代码前加上`try`表示这个代码可以跑出错误,在`catch`代码块中,错误会默认以`error`名传递给`catch`,也可以自己给一个名字`catch(myError)`
```swift
do {
    let printerResponse = try send(job: 1040, toPrinter: "Bi Sheng")
    print(printerResponse)
} catch {
    print(error)
}
```
可以使用多个catch来处理不同的错误
```swift
do {
    let printerResponse = try send(job: 1440, toPrinter: "Gutenberg")
    print(printerResponse)
} catch PrinterError.onFire {
    print("I'll just put this over here, with the rest of the fire.")
} catch let printerError as PrinterError {
    print("Printer error: \(printerError).")
} catch {
    print(error)
}
```
第二种使用`try?`来把结果转成`optional`类型。如果函数返回了错误,那么错误信息会被丢弃,并且结果为nil。否则会包含函数返回值。
```swift
let printerSuccess = try? send(job: 1884, toPrinter: "Mergenthaler")
let printerFailure = try? send(job: 1885, toPrinter: "Never Has Toner")
```
使用`defer`来写一段代码,这段代码会在函数,函数所有代码执行完成后执行,即在函数返回之前执行,不论这个函数是是否抛出错误,这段代码都会执行。可以用`defer`来做设置以及清除设置。
```swift
var fridgeIsOpen = false
let fridgeContent = ["milk", "eggs", "leftovers"]

func fridgeContains(_ food: String) -> Bool {
    fridgeIsOpen = true
    defer {
        fridgeIsOpen = false
    }

    let result = fridgeContent.contains(food)
    return result
}
fridgeContains("banana")
print(fridgeIsOpen)
```
### 泛型
使用`<name>`创建,泛型函数或者类型。(泛型到底是个啥,后面讲解)
```swfit
func makeArray<Item>(repeating item: Item, numberOfTimes: Int) -> [Item] {
    var result = [Item]()
    for _ in 0..<numberOfTimes {
        result.append(item)
    }
    return result
}
makeArray(repeating: "knock", numberOfTimes:4)
```
你也可以创建泛型函数,方法,枚举,类,结构体
```swift
// Reimplement the Swift standard library's optional type
enum OptionalValue<Wrapped> {
case None
    case Some(Wrapped)
}
var possibleInteger: OptionalValue<Int> = .None
possibleInteger = .Some(100)
```
在类型后面使用`where`来给出依赖列表
```swift
func anyCommonElements<T: Sequence, U: Sequence where T.Iterator.Element: Equatable, T.Iterator.Element ==             U.Iterator.Element>(_ lhs: T, _ rhs: U) -> Bool {
    for lhsItem in lhs {
        for rhsItem in rhs {
            if lhsItem == rhsItem {
                return true
            }
        }
    }
    return false
}
anyCommonElements([1, 2, 3], [3])
```
## swift详解
