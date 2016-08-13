# swift3 官方beta [2016-08-13](https://swift.org/documentation/#the-swift-programming-language)
# 欢迎来到swift世界
## swift快速入门
通常我们学习第一门语言的时候都会来个‘hello world’。在swift中只需
> print("hello world")

如果你写过C或者OC的话，会觉得他们看起来很像。在swift中不需要导入额外的库，如input/output或者string处理函数。这行代码就是程序的入口，swift不需要main()函数，（类似于python）也不需要在末尾添加“;”。  
这个教程会通过大量编程实例，来告诉你关于swift编程的知识。如果不明白的话，不要紧，本书的后面会有非常详细的介绍。  
### 简单值
通过`let`定义常量，`var`定义变量。在编译时不需要知道常量的值，但是你必须在定义时就给他赋值。  
> var myVariable = 42  
> myVariable = 50  
> let myConstant = 42

给变量赋值时，值得类型，必须与变量类型一致。然而你并不需要在定义变量是都制定类型，swift会自动判断值的类型，并推导出变量的类型。例如：`myVariable`是整型，因42就是整型。  
如果值不能确定类型的话，你可以定义变量的类型
> let explicitDouble: Double = 70  

数值不会被自动的转换为其他类型。如果你需要将一个数值转换为其他类型，你需要生成该类型的对象。
> let label = "The width is"  
> let width = 94  
> let widthLabel = label + String(width)  

swift提供了非常简便的方法来将变量包含到字符串中。你只需要使用`\(变量名)`。
> let apples = 3  
> let oranges = 5  
> let appleSummary = "I have \\(apples) apples."  
> let fruitSummary = "I have \\(apples + oranges) pieces of fruit.”  
