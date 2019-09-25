# JDK 1.8 新特性

## 一、函数式接口

**函数式接口**（**functional interface 也叫功能性接口**，其实是同一个东西）。简单来说，函数式接口是只包含一个方法的接口。比如Java标准库中的java.lang.Runnable和 java.util.Comparator都是典型的函数式接口。
java 8提供 @FunctionalInterface作为注解,这个注解是非必须的，只要接口符合函数式接口的标准（即只包含一个方法的接口），虚拟机会自动判断， 但 最好在接口上使用注解@FunctionalInterface进行声明，以免团队的其他人员错误地往接口中添加新的方法。

Java中的lambda无法单独出现，它需要一个函数式接口来盛放，lambda表达式方法体其实就是函数接口的实现.

下面的接口就是一个函数式接口

``` java

@FunctionalInterface //添加此注解后，接口中只能有一个抽象方法。
public interface A {
	void call();

}
```

## 二、lambda语法

包含三部分：
1、一个括号内用逗号分隔的形式参数，参数是函数式接口里面方法的参数
2、一个箭头符号：->
3、方法体，可以是表达式和代码块。

``` java
(parameters) -> expression 或者 (parameters) -> { statements; } 
```

通过下面的代码可以看到lambda表达式设计的代码更简洁，而且可读性更好。

``` java
public class Demo1 {
	public static void main(String[] args) {
		runThreadByLambda();
		runThreadByInnerClass();
	}

	public static void runThreadByLambda() {
		/*
		 Runnable就是一个函数式接口：他只有一个方法run()方法。
		 1、因为run()方法没有参数，所以   ->前面的()中不需要声明形参
		 2、run返回的是void，所以不需要return。
		 3、->后面写的代码其实就是定义在run方法内的代码。因为此处代码只有一行，所以{}也可以省略。如果此处多与一行，则无法省略。
		 */
		Runnable runnable = () -> System.out.println("这个是用拉姆达实现的线程");
		new Thread(runnable).start();
	}

	public static void runThreadByInnerClass() {
		Runnable runnable = new Runnable() {

			@Override
			public void run() {
				System.out.println("这个是用内部类实现的线程");

			}
		};
		new Thread(runnable).start();
	}
}
```

## 三、方法引用

其实是lambda表达式的一种简化写法。所引用的方法其实是lambda表达式的方法体实现，语法也很简单，左边是容器（可以是类名，实例名），中间是"::"，右边是相应的方法名。如下所示：

``` java
ObjectReference::methodName
```

一般方法的引用格式:

1. 如果是**静态方法**，则是ClassName::methodName。如 Object ::equals
2. 如果是**实例方法**，则是Instance::methodName。如Object obj=new Object();obj::equals;
3. **构造函数**.则是ClassName::new

``` java
public class Demo2 {

	public static void main(String[] args) {
		/*
		 * 方法引用
		 */
		Runnable runnable = Demo2::run;
		new Thread(runnable).start();
	}
	
	public static void run(){
		System.out.println("方法引用的代码...");
	}
}
```

可以看出，doSomething方法就是lambda表达式的实现，这样的好处就是，如果你觉得lambda的方法体会很长，影响代码可读性，方法引用就是个解决办法

## 四、默认方法—接口改进

简单说，就是接口可以有实现方法，而且不需要实现类去实现其方法。只需在方法名前面加个default关键字即可。

``` java
@FunctionalInterface
public interface A {
	void call();

	default void fun() {
		System.out.println("我是接口的默认方法1中的代码");
	}

	default void fun2() {
		System.out.println("我是接口的默认方法2中的代码");
	}
}
```

为什么要有这个特性？首先，之前的接口是个双刃剑，好处是面向抽象而不是面向具体编程，缺陷是，当需要修改接口时候，需要修改全部实现该接口的类，目前的 java 8之前的集合框架没有foreach方法，通常能想到的解决办法是在JDK里给相关的接口添加新的方法及实现。然而，对于已经发布的版本，是没法在给接口 添加新方法的同时不影响已有的实现。所以引进的默认方法。他们的目的是为了使接口没有引入与现有的实现不兼容发展。

***java8中接口和抽象类的区别***

**形同点：**
1.都是抽象类型；
2.都可以有实现方法（以前接口不行）；
3.都可以不需要实现类或者继承者去实现所有方法，（以前不行，现在接口中默认方法不需要实现者实现）

**不同点:**
1.抽象类不可以多重继承，接口可以（无论是多重类型继承还是多重行为继承）；
2.抽象类和接口所反映出的设计理念不同。其实抽象类表示的是"is-a"关系，接口表示的是"like-a"关系；
3.接口中定义的变量默认是public static final 型，且必须给其初值，所以实现类中不能重新定义，也不能改变其值；抽象类中的变量默认是 default 型，其值可以在子类中重新定义，也可以重新赋值。

``` 
总结：默认方法给予我们修改接口而不破坏原来的实现类的结构提供了便利，目前java 8的集合框架已经大量使用了默认方法来改进了，当我们最终开始使用Java 8的lambdas表达式时，提供给我们一个平滑的过渡体验。也许将来我们会在API设计中看到更多的默认方法的应用。
```

## 五、使用lambda改进的集合框架

### 5.1 集合中内部迭代

``` java
import java.util.ArrayList;
import java.util.List;

public class Demo3 {
	public static void main(String[] args) {
		List<User> users = new ArrayList<User>();
		users.add(new User(20, "张三"));
		users.add(new User(22, "李四"));
		users.add(new User(10, "王五"));

		users.forEach((User user) -> System.out.println(user.getAge()));
	}
}

```

### 5.2 Stream API

``` 
流（Stream）仅仅代表着数据流，并没有数据结构，所以他遍历完一次之后便再也无法遍历（这点在编程时候需要注意，不像Collection，遍历多少次里面都还有数据），它的来源可以是Collection、array、io等等。
```

```
流作用是提供了一种操作大数据接口，让数据操作更容易和更快。它具有过滤、映射以及减少遍历数等方法，这些方法分两种：中间方法和终端方法，“流”抽象天生就该是持续的，中间方法永远返回的是Stream，因此如果我们要获取最终结果的话，必须使用终点操作才能收集流产生的最终结果。区分这两个方法是看他的返回值，如果是Stream则是中间方法，否则是终点方法。
```

#### filter

在数据流中实现过滤功能是首先我们可以想到的最自然的操作了。Stream接口暴露了一个filter方法，它可以接受表示操作的Predicate实现来使用定义了过滤条件的lambda表达式。

``` java
import java.util.stream.Stream;

public class StreamDemo {
	public static void main(String[] args) {
		List<User> users = new ArrayList<User>();
		users.add(new User(20, "张三"));
		users.add(new User(22, "李四"));
		users.add(new User(10, "王五"));
		
		Stream<User> stream = users.stream();
		stream.filter(p -> p.getAge() > 20); //过滤年龄大于20的
	}
}
```

#### map

假使我们现在过滤了一些数据，比如转换对象的时候。Map操作允许我们执行一个Function的实现（Function<T,R>的泛型T,R分别表示执行输入和执行结果），它接受入参并返回。

``` java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Stream;

public class StreamDemo {
	public static void main(String[] args) {
		List<User> users = new ArrayList<User>();
		users.add(new User(20, "张三"));
		users.add(new User(22, "李四"));
		users.add(new User(10, "王五"));
		
		Stream<User> stream = users.stream();
		 //所有的年龄大于20岁的User对象，转换为字符串50对象。现在流中只有字符串对象了。
		stream.filter((User user) ->  user.getAge() > 20).map((User user) -> {return "50";});
	}
}
```

#### count

count方法是一个流的终点方法，可使流的结果最终统计，返回long

``` java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collector;
import java.util.stream.Stream;

public class StreamDemo {
	public static void main(String[] args) {
		List<User> users = new ArrayList<User>();
		users.add(new User(20, "张三"));
		users.add(new User(22, "李四"));
		users.add(new User(10, "王五"));
		
		Stream<User> stream = users.stream();
		long count = stream.filter((User user) ->  user.getAge() >= 20).map((User user) -> {return "50";})
		.count(); //返回流中元素的个数。
		System.out.println(count);	
	}
}

```

# JDK 1.8 新特性之Stream

首先对stream的操作可以分为两类，**中间操作(intermediate operations)和结束操作(terminal operations):**

中间操作总是会惰式执行，调用中间操作只会生成一个标记了该操作的新stream。
结束操作会触发实际计算，计算发生时会把所有中间操作积攒的操作以pipeline(流水线)的方式执行，这样可以减少迭代次数。计算完成之后stream就会失效。

虽然大部分情况下stream是容器调用Collection.stream()方法得到的，但stream和collections有以下不同：

- 无存储。stream不是一种数据结构，它只是某种数据源的一个视图，数据源可以是一个数组，Java容器或I/O channel等。
- 为函数式编程而生。对stream的任何修改都不会修改背后的数据源，比如对stream执行过滤操作并不会删除被过滤的元素，而是会产生一个不包含被过滤元素的新stream。
- 惰式执行。stream上的操作并不会立即执行，只有等到用户真正需要结果的时候才会执行。
- 可消费性。stream只能被“消费”一次，一旦遍历过就会失效，就像容器的迭代器那样，想要再次遍历必须重新生成。

