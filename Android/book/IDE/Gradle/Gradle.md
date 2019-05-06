# Gradle

## 简介
Gradle是一个基于Apache Ant和Apache Maven概念的项目自动化建构工具。它使用一种基于Groovy的特定领域语言来声明项目设置，而不是传统的XML。
当前其支持的语言限于Java、Groovy和Scala，计划未来将支持更多的语言。
——来自[维基百科](https://zh.wikipedia.org/zh-hans/Gradle)

## Groovy基础
Groovy是基于JVM虚拟机的一种动态语言，它的语法和Java非常相似，Groovy可以完全兼容Java，即可以在build脚本中写任何的Java代码，又在此基础上增加了很多动态类型和灵活的特性，比如支持闭包、DSL，可以说它是一门非常灵活的动态脚本语言。   
咱们这里主要了解一些简单的概念：字符串、集合、方法，这些也是所有语法最基础的部分，可以更好的帮助我们了解Groovy语法，为后期Gradle的学习和使用打下个基础。
### 字符串 ###
首先说明一下，在Groovy中分号不是必需的，与Java不同Groovy没有这个强制规定，所以Gradle脚本中很多都没有分号，这个是Groovy的特性。   
在Groovy中，**单引号**和**双引号**
都可以定义一个字符串常量，不同的是**单引号**标记的是纯粹的字符串常量，**双引号**除此之外还可以直接进行表达式运算。   
```groovy
task printStringClass {
    def strl = '单引号'
    def str2 = "双引号"
    println "单引号定义的字符串类型："+ strl.getClass().name
    println "双引号定义的字符串类型："+ strl.getClass().name
}
```
使用./gradlew printStringClass运行后可以看到：
```java
单引号定义的字符串类型： java.lang.String
双引号定义的字符串类型： java.lang.String
```
可以看出单引号和双引号输出的结果并没有区别，而针对于上面说到的表达式运算：
```groovy
task printStringVar {
    def var = 'Gavin'

    println "单引号的变量计算：${var}"
    println "双引号的变量计算：${var}"
}
```
./gradlew printStringVar 运行后输出：  
```
单引号的变量计算：${name}   
双引号的变量计算：张三
```
从结果可以看出单引号是直接输出，而双引号输出的是运算后的结果，我们讲可以通过双引号来进行表达式计算和字符串的拼接。   

### 集合 ###
Java中的集合在开发中经常被我们使用到，而Groovy完全兼容了Java的集合，并且进行了扩展，使得集合的声明和使用及其简单。在这里我们简单的介绍一下List和Map，如果大家想深入了解可以参考[Groovy working with collections](http://docs.groovy-lang.org/next/html/documentation/working-with-collections.html#JN1035-Maps-Collectionviewsofamap)。

-  #### List ####
您可以创建如下List。注意，[]是空列表表达式。

```groovy
task printList {
    //我们定义一个由逗号分隔、方括号包围的列表号，并将该List分配给一个变量
    def numbers = [1, 2, 3, 4, 5, 6]   
    //该集合是Java的Java.util.List的实例     
    println "List type:" + numbers.getClass().name
    //可以使用size()方法查询List的长度
    println "List size:" + numbers.size()
    //获取索引第二位的元素,List的索引也是从0开始
    println "List index 2:" + numbers[1]
    //获取索引最后一位的元素
    println "List index the last one:" + numbers[-1]
    //获取索引倒数第二位的元素
    println "List index the second-to-last:" + numbers[-2]
    //获取索引1和3的元素集合
    println "List index 1 and index3 : ${numbers[1,3]}"
    //获取索引2~4的元素集合
    println "List index 2~4 : ${numbers[2..4]}"
}
```   
./gradlew printList 运行后输出：
```
List type:class java.util.ArrayList
List size:6
List index 2:2
List index the last one:6
List index the second-to-last:5
List index 1 and index3 : [2, 4]
List index 2~4 : [3, 4, 5]
```
Groovy可以通过索引的方式进行访问，就像数组一样，除此之外，还提供了负索引和范围索
引。负索引代表从右边开始，-1就代表从右侧数第一个，-2代表从右侧数第二个，以此
类推： 1..3 这种是一个范围索引，中间用两个“ ．”分开， 这个会经常遇到。
此外，Groovy使用each方法为List提供了非常方便的迭代操作，
```groovy
	...
	numbers.each{
		//it 代表遍历出来的每个元素
		println it
	}
```
- #### Map ####
Map用法很多跟List比较像，在Groovy中可以使用map文字语法[:]:创建映射(也称为关联数组):
```groovy
task printMap {
    def map = ['name': 'Gromit', 'likes': 'cheese', 'id': 1234]
    //该集合是Java的java.util.LinkedHashMap的实例     
    println "Map type : ${map.getClass().name}"
    //通过size()过去Map长度
    println "Map size : ${map.size()}"
    //通过映射关系来获取指定key的value值
    println "Map name : ${map.name}"
    //通过类似数组的方法来获取指定key的value值
    println "Map likes : ${map['likes']}"
    //通过get方法来获取指定key的value值
    println "Map id : ${map.get("id")}"
    
    //遍历Map 可以通过key和value属性获取每个元素的key和value
    //只不过被送代的元素是一个Map.Entry的实例
    map.each {
	println "Key:${it.key}, Value:${it.value}"
    }
}
```
关于集合的使用就简单的说到这里，可以看出在Groovy中集合的使用时非常简单非常灵活，大家如果有兴趣可以参考[Groovy working with collections](http://docs.groovy-lang.org/next/html/documentation/working-with-collections.html#JN1035-Maps-Collectionviewsofamap)。

### 方法 ###
对于方法的调用，在Groovy中也比java更加简洁，在这里简单的说一下Groovy和Java使用方法的不同.
- 方法使用的简洁
- return可以省略
- 代码块可以当作参数传递，某些时候可以省略
```groovy
//方法使用上的简洁
task invokeMethod {
    methodPlus(1,2)
    //在Groovy中更加简洁的用法 省略括号
    methodPlus 1,2
    def add1 = method2 2,3
    def add2 = method2 5,4
    println "max value : " + add1
    println "max value : " + add2
}

def methodPlus(int a, int b){
    println a+b
}

//return 可以省略
def method2(int a, int b){
    if(a > b){
	a
    }else{
	b
    }
}

//代码块 可以当作参数传递

```
所以到这里，Groovy基础就先简单聊到这里，如果大家想深入了解可以参考[Gradle Docs](http://groovy-lang.org/single-page-documentation.html)。

## Gradle构建脚本基础 ##
从这章开始正式介绍一下Gradle，主要会从四个大方面介绍Gradle,**Gradle基础概念**、**Project**、**Task**以及**自定义属性**，帮助大家能够快速入门Gradle。

1. ### 基础概念 ###

在Gradle中，有两个文件最常见：**Settings.gradle**和**build.gradle**.
- Settings.gradle   
settings文件是Gradle定义的一个设置文件，用于初始化和配置工程。设置文件的默认名字是settings.gradle，放在根工程目录下。   
如果一个工程下有多个子工程，只有在Settings文件里配置的子工程才能被Gradle识别，在构建的时候才能被包含进去。  
例如下面的Settings.gradle文件
```groovy
//Google Firebase Sample
include ':admob:app',':analytics:app',':app-indexing:app',':auth:app',
        ':config:app',':crash:app',':database:app',':dynamiclinks:app',
        ':firestore:app',':functions:app',':internal:chooser',
        ':internal:lint',':internal:lintchecks', ':inappmessaging:app',
        ':messaging:app',':mlkit:app',':mlkit-langid:app',
        ':perf:app',':storage:app'
//General App Sample
include ':app', ':data', ':common'
```
从Firebase的示例可以看出，项目中定义了很多子工程，每个子工程都有一个app的module项目，所以在配置的时候会指定相应目录和相应module项目，这样就能实现多个子工程的gradle初始化。  
从下面的示例可以看出，如果不指定目录，默认目录是其同级目录，例如':app'。  
**利用这个特性，我们可以把我们的项目放在任何目录下，只要在Settings文件中指定好路径，就可以非常灵活的对项目进行分级、分类。**
