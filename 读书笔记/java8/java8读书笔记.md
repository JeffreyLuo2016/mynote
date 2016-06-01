# java8读书笔记
## 第十三章
### assert

1. **为了在运行时启用断言检查，必须指定 *-ea* 选项。例如，为了启用针对AssertDemo的断言，使用下面这行命令执行程序:**    
	`java -ea AssertDemo`

2. **assert关键字的两种形式：**
    - `assert condition;`, 其中condition是一个求值结果必须为布尔型的表达式。如果为true，那么断言为真，不会发生其他动作。 如果条件为false，那么断言失败并抛出默认的 *AssertionError* 对象。
    - `assert condition: expr;` 在这个版本中，expr是传递给AssertError构造函数的值。这个值被转换成相应的字符串格式，并且，如	果断言失败，将会显示该字符串。典型地，将为expr指定一个字符串，不过也可以指定任何 *非void* 表达式，只要定义了合理的字符串转换即可。
    
3. **启用和禁用断言**  
    当执行代码时，可以使用 *-da* 选项禁用所有的断言。 通过在 *-ea* 或 *-da* 选项后面指定包的名称，并在后面跟上3个点。可以启用或禁用指定包（及其所有子包）中的断言。 
    例如:启用mypack包中的断言 `-ea:mypack...`， 禁用mypack包中的断言 `-da:mypack...` 。 还可以为-ea或-	da选项指定具体的某个类，如启用AssertDemo类中的断言：`-ea:AssertDemo` 。  

> 关于断言需要理解的重要一点是：不能依赖他们执行程序实际需要的任何动作。因为在正常情况下， 发布代码在运行时会禁用断言。例如：`assert(n = getnum());` , 如果启用了断言这句代码可以工作得很好，反之则`n = getnum()`不会执行。 **不需要从发布代码中移除assert语句**。

###静态导入

静态导入扩展了import关键字的功能。通过在import后面添加static关键字，可以使用import语句导入类或接口的静态成员。当使用静态导入时，可以直接
通过名称引用静态成员，而不必使用他们的类名进行限定，从而简化并缩短使用静态成员所需的语法。   

- 静态导入的两种形式
    - `import static pkg.type-name.static-member-name;`
    - `import static pkg.type-name.*;`

### 通过this()调用重载的构造函数

当使用重载的构造函数时，在一个构造函数中调用另一个构造函数有时是有用的。在java中，这是通过使用this关键字的另外一种形式完成的：`this(arg-list)`。*在构造函数中对this()的调用必须是第一条语句*。     
使用this()需要牢记两条限制：首先，在调用this()时不能使用当前类的任何实例变量。其次，在同一个构造函数中不能同时使用super()和this()，因为他们都必须是构造函数中的第一条语句。      

## 第十四章 泛型

- 就本质而言，术语"范型"的意思是参数化类型。      
- 编译范型类时，编译器移除所有泛型信息，进行必要的类型转换，从而使代码的行为看起来好像是创建了特定版本的带范型参数的类一样。移除泛型类型信息的过程被称为擦除。    
- 创建类型安全的代码，从而在编译时能够捕获类型不匹配错误，这是泛型的一个关键优势。尽管使用Object引用创建"泛型"代码总是可能的，但这类代码不是类型安全的，并且对它们的误用会导致运行时异常。泛型可以防止这种问题的发生。本质上，通过泛型可以将运行时错误转换成编译时错误，这是泛型的主要优势。
- 在泛型中可以声明多个类型参数。为了指定两个或更多个类型参数，只需要使用逗号分隔参数列表即可。
- 泛型的一般形式	
声明泛型的语法: `class class-name<type-param-list> { // ...`       
声明指向泛型的引用: `class-name<type-arg-list> var-name = new class-name<type-arg-list> (cons-arg-list);`
- 有界类型：`<T extends superclass>`, 除了使用类作为边界之外，也可以使用接口。实际上，可以指定多个接口作为边界。此外，边界可以包含一个类和一个或多个接口。对于这种情况，必须首先指定类类型。如果边界包含接口类型，那么只有实现了那种接口的类型参数是合法的。当指定具有一个类和一个接口或多个接口的边界时，使用&运算符连接它们。例如: `class Gen<T extends MyClass & MyInterface> { //...`.
- 一般来说，要为通配符建立上界，可以使用如下的通配符表达式：     
    `<? extends superclass>`    
    其中，superclass是作为上界的类的名称。记住，这是一条包含语句，因为形成上界（由superclass指定的边界）的类也位于边界之内。      
    还可以通过为通配符添加一条super子句，为通配符指定下界。下面是一般形式：  
    `<? super subclass>`        
    对于这种情况，只有subclass的超类是可接受参数。这是一条排除子句，因此与subclass指定的类不匹配。     
- 泛型方法的语法：`<type-param-list> ret-type meth-name(param-list) { //...`, 对于所有情况，type-param-list是由逗号分隔的类型参数列表。注意对于泛型方法，***类型参数列表位于返回类型之前***。
- 泛型构造函数

    ```java
    // Use a generic constructor. 
    class GenCons { 
      private double val; 

      <T extends Number> GenCons(T arg) { 
        val = arg.doubleValue(); 
      } 
     
      void showval() { 
        System.out.println("val: " + val); 
      } 
    } 
     
    class GenConsDemo { 
      public static void main(String args[]) { 
     
        GenCons test = new GenCons(100); 
        GenCons test2 = new GenCons(123.5F); 
     
        test.showval(); 
        test2.showval(); 
      } 
    }
    ```

- 泛型接口的通用语法:  
`interface interface-name<type-param-list>{ //...`      
在此，type-param-list是由逗号分隔的类型参数列表。当实现泛型接口时，必须指定泛型类型，如下所示:     
`class class-name<type-param-list> implements interface-name<type-arg-list>{`   

- 原始类型与遗留代码
  - 为了处理泛型过渡，JAVA允许使用泛型而不提供任何类型参数。这会为类创建原始类型(raw type)。这种原始类型与不使用泛型的遗留代码是兼容的。
  - 本质上，这会创建使用Object替换类型T的XX对象。

- 泛型类层次
  - 泛型和非泛型之间的关键区别是：在泛型层次中，类层次中的所有子类都必须向上传递超类所需要的所有类型参数。这与必须沿着类层次向上传递构造函数的参数类似。
  - 即使泛型超类的子类不必泛型化，也仍然必须指定泛型超类所需要的类型参数。

- 泛型层次中的运行时类型比较   
请记住，在运行时不能使用泛型类型信息。

- 泛型的类型推断   
当使用类型推断时，用于泛型引用和实例创建的声明语法具有如下所示的一般形式  
`class-name<type-arg-list> var-name = new class-name<>(cons-arg-list);`     
也可以为参数传递应用类型推断。如方法： 

  ```java
  boolean isSame(MyClass<T, V> o){
    if(ob1 == o.ob1 && ob2 == o.ob2){
        return true;
    }else{
      return false;
    }
  }
  ```

  那么，下面的调用是合法的：

    ```java
    if(mcOb.isSame(new MyClass<>(1, "test"))) System.out.println("Same");
    ```

  在这个例子中，可以推断传递给isSame()方法的类型参数。  

- 擦除  
擦除的工作原理如下：编译java代码时，所有泛型信息被移除（擦除）。 这意味着使用它们的界定类型替换类型参数，如果没有显示地指定界定类型，就使用Object，然后应用适当的类型转换（根据类型参数而定），以保持与类型参数所指定类型的兼容性。编译器也会强制实现这种类型兼容性。使用这种方式实现泛型，意味着在运行时没有类型参数。他们只是一种源代码机制。   
- 模糊性错误
泛型的引入，增加了引起一种新类型错误——模糊性类型错误，必须注意防范。当擦除导致两个看起来不同的泛型声明，在擦除之后变成相同的类型而导致冲突时，就会发生模糊性错误。    

通常，模糊性错误的解决方案涉及调整代码结构，因为模糊性通常意味着在设计中存在概念性错误。    

- 使用泛型的一些限制
  1. 不能实例化类型参数
  2. 静态成员不能使用在类中声明的类型参数
  3. 对泛型数组的限制
      1. 不能实例化元素类型为类型参数的数组。
      2. 不能创建特定类型的泛型引用数组。 
          - JAVA为什么不让创建泛型数组
            首先，我觉得定制java标准的那些人完全可以让java创建泛型数组；只是他们权衡了一下，觉得还是禁止了的好，一下就说说我的揣测：    
            如果我们写如下代码是没有问题的：   

            ```java
            List<String> a = new ArrayList<String>();
            ```
            
            那么为嘛到了数组就不行呢：   
           
            ```java
            List<String>[] arr = new ArrayList<String> [10];
            ```
            我们先来看看数组变量和普通变量的区别：数组变量arr和普通变量a都在栈中，但是呢！arr[0]在堆中。所以呢？所以我们有下面一个例子：   
            
            ```java
            List<String>[] arr = new ArrayList<String> [10];
            Object[] orr = arr;
            List<StringBuffer> buf = new ArrayList<StringBuffer> ();
            orr[0] = buf;
            List<String> str = arr[0];
            String val = str.getValue();
            ```

            可以发现，堆中的第0个元素是List<StringBuffer>了，但是arr[0]还是指向了它，如果是普通变量，这是不可能的；所以，即便是引入了泛型，也不是安全的；最后一句，由于编译器会加上string转化，造成ClassCastException；泛型本来就是为了安全，如果不能保证数组的安全，为啥还有多此一举呢？     
            不过，如果使用通配符的话，可以创建指向泛型类型的引用数组，如下所示：  
            `Gen<?> gens[] = new Gen<?>[10]; //OK`
            
- 对泛型异常的限制  
  泛型不能扩展Throwable，这意味着不能创建泛型异常类。    


## 第十五章 lambda表达式 
