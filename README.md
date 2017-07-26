# StreamAPI
Stream 是 Java8 中处理集合的关键抽象概念，它可以指定你希望对
集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。
使用Stream API 对集合数据进行操作，就类似于使用 SQL 执行的数
据库查询。也可以使用 Stream API 来并行执行操作。简而言之，
Stream API 提供了一种高效且易于使用的处理数据的方式。
<!--more-->
## 什么是Stream
流(Stream) 到底是什么呢？
是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列。
集合讲的是数据，流讲的是计算！

注意：

①Stream 自己不会存储元素。

②Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。

③Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。
## 操作的步骤
1.创建Stream
一个数据源（如：集合、数组），获取一个流 
```
由值创建流
Stream<String> stream_string=Stream.of("a","b","c");

由数组创建流
int[] array=new int[10];
IntStream stream_array=Arrays.stream(array);

由Collection集合创建
List<String> list=new ArrayList<>();
Stream<String> stream=list.stream();

无限流的创建
//迭代
Stream<Integer> stream4=Stream.iterate(0, (x) -> x+2);
//生成
Stream.generate(()->Math.random());

```
2.中间操作
一个中间操作链，对数据源的数据进行处理 

```
List<Employee> emps = Arrays.asList(
			new Employee(102, "李四", 59, 6666.66),
			new Employee(101, "张三", 18, 9999.99),
			new Employee(103, "王五", 28, 3333.33),
			new Employee(104, "赵六", 8, 7777.77),
			new Employee(104, "赵六", 8, 7777.77),
			new Employee(104, "赵六", 8, 7777.77),
			new Employee(105, "田七", 38, 5555.55)
	);
    
filter——接收 Lambda ， 从流中排除某些元素。 
emps.parallelStream().filter((e) -> e.getSalary() >= 5000).forEach(System.out::println);

limit——截断流，使其元素不超过给定数量。
emps.parallelStream().filter((e) -> e.getSalary() >= 5000).limit(2).forEach(System.out::println);

skip(n) —— 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit(n) 互补
emps.parallelStream().filter((e) -> e.getSalary() >= 5000).skip(2).forEach(System.out::println);

distinct——筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素
emps.stream().distinct().forEach(System.out::println);
    
映射
map——接收 Lambda ， 将元素转换成其他形式或提取信息。接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。
flatMap——接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流
Stream<String> str = emps.stream().map((e) -> e.getName());
public static Stream<Character> filterCharacter(String str){
		List<Character> list = new ArrayList<>();
		
		for (Character ch : str.toCharArray()) {
			list.add(ch);
		}
		
		return list.stream();
	}
    
Stream<Stream<Character>> stream2 = strList.stream()
.map(TestStreamAPI1::filterCharacter);
               
Stream<Character> stream3 = strList.stream().flatMap(TestStreamAPI1::filterCharacter);

sorted()-- 自然排序
sorted(Comparator com)-- 定制排序
emps.stream().map(Employee::getName).sorted().forEach(System.out::println);

emps.stream().sorted((x, y) -> {
		if(x.getAge() == y.getAge()){
		return x.getName().compareTo(y.getName());
		}else{
		return Integer.compare(x.getAge(), y.getAge());
				}
			}).forEach(System.out::println);
```
3.终止操作
一个终止操作，执行中间操作链，并产生结果
```
allMatch——检查是否匹配所有元素
boolean bl = emps.stream().allMatch((e) ->e.getStatus().equals(Status.BUSY));

anyMatch——检查是否至少匹配一个元素
boolean bl1 = emps.stream().anyMatch((e) -> e.getStatus().equals(Status.BUSY));

noneMatch——检查是否没有匹配的元素
boolean bl2 = emps.stream().noneMatch((e) -> e.getStatus().equals(Status.BUSY));

findFirst——返回第一个元素
emps.stream().sorted((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary())).findFirst();

findAny——返回当前流中的任意元素
Optional<Employee> op2 = emps.parallelStream().filter((e) -> e.getStatus().equals(Status.FREE)).findAny();

count——返回流中元素的总个数
long count = emps.stream().filter((e) -> e.getStatus().equals(Status.FREE)).count();

max——返回流中最大值
Optional<Double> op = emps.stream().map(Employee::getSalary).max(Double::compare);

min——返回流中最小值
Optional<Employee> op2 = emps.stream().min((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()));

归约reduce 可以将流中元素反复结合起来，得到一个值
List<Integer> list=Arrays.asList(1,2,3,4,5,6,7,8,9,10);		
Integer sum = list.stream().reduce(0, (x, y) -> x + y);

collect——将流转换为其他形式。接收一个 Collector接口的实现，用于给Stream中元素做汇总的方法
List<String> list = emps.stream().map(Employee::getName).collect(Collectors.toList());
HashSet<String> hs = emps.stream().map(Employee::getName).collect(Collectors.toCollection(HashSet::new));

```
感觉JDK1.8的Stream和lambda都接近Scala语法
{% link Scala http://lishspark.github.io/2016/06/16/SparkSQL-DataFrame/ true Scala %}

