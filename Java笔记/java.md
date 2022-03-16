

String用法

https://blog.csdn.net/sjq__python_web/article/details/80099454

注解

https://www.jianshu.com/p/9471d6bcf4cf

Spring Boot之工程结构推荐
https://blog.csdn.net/suifeng629/article/details/106149367/

spring boot中不能识别RestController的原因

https://blog.csdn.net/qq_16739693/article/details/80271987?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242

jsr-303

https://www.jianshu.com/p/554533f88370

@RestController，@Controller， @ResponseBody以及thymeleaf页面的搭配使用

https://blog.csdn.net/qq_36838406/article/details/100867004

配置IDEA自带的Maven的镜像源

https://blog.csdn.net/qq_39438086/article/details/107515291

@service 和 @repository

https://blog.csdn.net/zhizhuodewo6/article/details/81365516

# bugs

## 数组比较

![image-20210307155143395](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210307155143395.png)

![image-20210307155154488](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210307155154488.png)

所以比较数组相等时，不能使用a.equals(b),而得用Arrays.equals(a,b)

# 基础用法

## package（包）

### * 的使用

- `import com.huawei.*`不能把孙子包、重孙子包……导入，只能导入此文件夹下的所有包。

## 数组

### 构造

> int[] ns = new int[5];  //int[] 为关键词
>
> double[] ns={1.2 , 2.0};
>
> 
>
> int[] [] a = new int [3] [2];  //二维数组
>
> int[] [] a = {{1,2}，{2,3}};

### 打印

- 一维数组

  ```
  int[] a={1,2,3};
  System.out.println(Arrays.toString(a));  //[1,2,3]
  System.out.println(Arrays.asList(a));    //[[I@762efe5d]
  ```

- 二维数组

  ```
  int[][] ans= {{1,3,3,1},{1}};
  System.out.println(Arrays.deepToString(ans)); //[[1, 3, 3, 1], [1]]
  System.out.println(Arrays.toString(ans));     //[[I@7637f22, [I@4926097b]
  System.out.println(Arrays.asList(ans));       //[[I@762efe5d, [I@5d22bbb7]
  ```

### 排序

> import java.util.Arrays;
>
> 
>
> Arrays.sort(ns);



判断字符串相等

> if (s1.equals(s2)) {
>         System.out.println("s1 equals s2");
> }

- 如果用s1==s2，则判断的是“指针”是否相同，则肯定不同。

for语句

> int[] ns = { 1, 4, 9, 16, 25 };
> for (int n : ns) {
>     System.out.println(n);
> }

- c++的用法也可以，还可以用类似“切片”的方式

## 类型转换

- 大的类型向小的类型转换的时候必须使用强制转换，java不允许高向低的隐式转换
- 4.5这样的小数，默认是double类型。只有加一个“f”，才可以表示float类型。所以必须使用 `float a=4.5f`。

## 类

### this关键字

- `this(name,weight);` 可以在一个构造函数里调用另外一个构造函数，且需要放在第一行。

### super关键字

- 类似于this，只不过super是指向父类的域，this是指向自己的域。所以super也可以用`super(int x,String y)`来调用父类的构造函数，且需要放在第一行。

```
在子类里调用父类的方法（构造函数也可以调用）
public void sayHello(){
	super.sayHello(); //调用子类的sayHello时，先调用父类的sayHello
}
```

### static

- 如果是static变量，不能用this关键字抽取它，得用`类名.变量名`

### final关键字

- 带有static的

 ```
  private static final int totalId=0; //第一次赋值机会
  //static final量的第二次赋值的机会
  static{
  	totalId=10;
  }
 ```

- 不带static的

```
private final int id=0; //第一次赋值的机会

//构造函数第二次
```

- 加fina关键字的，在编译的时候，会把那个常量直接写入.class文件。
- 一般使用static final修饰常量

### abstract 关键字（抽象）

- 拥有抽象方法的类，必须标注抽象关键字。但是抽象类中不一定有抽象方法。
- 抽象类中的方法不能有实现。

- 继承抽象类，如果子类依旧是抽象类，则不需要全部实现（override、重写）；但如果子类是实体类，则需要把所有抽象方法全部实现（override、重写）。

### interface（接口）

- jdk1.8之后，接口里的函数可以有实现。

### 继承（构造函数）

总得执行一次父类的构造函数。

- 子类会默认调用父类的无参构造函数（无论写不写`super()`，都会默认调用）
- 如果显示的调用了`super(……)`，那么这就不会再默认调用了。
- 如果父类中没有**无参构造函数**，那子类中的构造函数，第一行，必须是`super(……)`，否则会报错
- 

### 继承（互相赋值）

- 可以把子孙类的对象赋值给祖宗类，反之不可（反过来需要强制类型转换，但依旧会在运行时出问题）。

- 可以把子孙类的对象赋值给祖宗接口，反之不可。

- ```
  //ex：只能
  String s="123";
  Object o=s;
  String s1=(String)o;
  ```

### instanceof

## 枚举类型

- ```
  public enum Direction{
  	EAST,SOUTH,WEST,NORTH;
  }
  Direction dir=Direction.NORTH; //其实等价于Direction dir="NORTH";优势是容易发现拼写错误
  ```

- 直接用.java格式建立就可以

## 函数传入参数（基本类型）

- 类，默认传入地址，而不是默认传入值。所以对于如下代码

```
Apple a=new Apple();
public void f(Apple a){
	a=new Apple();
	a=……	//外边的a不受影响
	……		//外边的a不受影响
}
```

- String、int、double、boolean、float 
  - 是定死的无法被改变的
  - 是无法按照地址传参的。
  - 需要被赋予初始值（实例化），才能使用。

## 多态

- 当一个子类和父类都拥有函数`f( )`和static函数`static_f()`的时候，执行时，效果不同：

```
//子类Apple 父类Fruit 都拥有 普通函数f() 和 static函数static_f()
public void f(Fruit x){
	x.f(); 			//执行apple的f()
	x.static_f(); 	//执行Friut的static_f()
}
```

## 异常

![image-20210322103829426](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210322103829426.png)

- ```
  try{
  	
  }catch(Exception e){
  
  }finally{
  
  }
  ```

- ![   ](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210322104504282.png)

- 有throw，就得有catch

- 几种异常

  - Error 
    - 程序员无法解决的问题（内存不足、找不到类……）
  - Exception（不算RuntimeException）
    - 处理可以预见的各种问题
  - RuntimeException
    - 由于输入根本就不合法导致的问题

- 正常的抛出异常需要在方法声明的最后加上`throws Exception`的声明。但是如果抛出Error或者RuntimeException的话，那也不需要这个声明。但是，就算声明了`throws Exception`，也不一定非得有throw，也就是“声明了抛，但是我可以不抛”。

- 至少要保证catch里的内容有可能被抛出。
  - 如果`catch SQLException`，就至少要保证try里能抛出他。
  - 如果`catch Exception`，因为RuntimeException是可以随时throw的，所以“看起来没可能抛出”也可以
  
- ”初始化问题“
  
  - 如果在赋值的时候发生了异常，那么这个变量的赋值是不成功的。如果恰巧此变量是局部变量，那么这个局部变量就未初始化，java会编译报错。
  
- 父类不抛异常，子类也不能抛。父类抛出小异常，子类抛出大异常，也不可以。

## string

# java集合类们





### 具体函数们

#### 遍历string

https://blog.csdn.net/qq_38749759/article/details/78945552

```
		Set hs=new HashSet();
		String[] words=s.split("");
		for (String each:words) {
			hs.add(each);
		} //这样也行
```

#### set

https://blog.csdn.net/DragonGirI/article/details/83388274

#### map

https://blog.csdn.net/taraex/article/details/90243965

#### string

https://blog.csdn.net/weixin_41477980/article/details/85109179



![image-20210329105108119](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210329105108119.png)

- 类似于所有的类都是Object，所有集合们也都是Collection（是一个接口）
- 只有实现了comparable接口的类中的方法`compareTo()`(`implements comparable`),才能放入collections.sort( )、treeset、treemap
- hash类的，用equals方法和hashCode方法，如果实现了equals方法，就一定要自己实现hashCode方法，且两者一定要一致。

## set

- 遍历set的三种方法
- ![image-20210329105333667](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210329105333667.png)
- 

## list

- 不要一边遍历、一边remove

![image-20210329104806323](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210329104806323.png)

## iterator

![image-20210329104942156](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210329104942156.png)

- **`it.next()`会自动往后走一个**，并返回此时的值！！！所以得先把`.next()`的返回值在一次循环中保存下来



## map

- HashMap和TreeMap

```
Map map=new HashMap();
map.put("1","Monday");
map.put("one","Monday");
```

- map的遍历
- 计算一句话中出现频率最高的词

```
		String str = "China will strengthen international cooperation on novel coronavirus epidemic control and continue to provide assistance within its ability to countries affected by the epidemic, Xi Jinping, general secretary of the Communist Party of China Central Committee, said on Wednesday.";

		Map map = new HashMap();
		String[] words=content.split(" ");
		for(int i=0;i<words.length;i++) { 
			String word = words[i];
			int count = 0;
			if (map.containsKey(word)) {
				count = (Integer) map.get(word);
			}
			map.put(word, count + 1);
		}
		Iterator it = map.keySet().iterator(); 
		//最初的时候，迭代器是指向-0.5的位置的，所以通过一个next()，可以把第一个元素包含进来
		int maxCount = 0;
		String maxKey = "";
		while (it.hasNext()) {
			String key = (String) it.next();
			if ((Integer) map.get(key) > maxCount) {
				maxKey = key;
				maxCount = (Integer) map.get(key);
			}
		}
```

## 泛型

- 优点：
  - 取值的时候，不用强制类型转换了
  - 编译时就能发现问题

```
Set<String>  set=new HashSet<String>(); 

List<String>  list=new ArrayList<String>(); 
list.add(“Tom”);                              //合法
String  name=list.get(0);        //合法，无需强制类型转换
Object  obj=list.get(0);           //合法，允许向上转型
//编译出错，无法类型转换
StringBuffer  sb=(StringBuffer)list.get(0); 
```

## comparator



# idea

## 快捷键

- ALT+Insert键：自动生成代码（构造、get（）等等）



# springboot

https://www.yuque.com/atguigu/springboot/rg2p8g

![image-20210312004005902](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210312004005902.png)

## 注解们

- @Component 

  声明为组件

- @Controller

  为控制器组件

- Service

  为业务逻辑组件

- Repository

  为数据库层组件

- @ComponentScan

  指定包扫描规则组件

- @Import

  

## 依赖们、工具

### lombok

可以自动生成构造函数、get函数、tostring函数等等

![image-20210225153201033](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210225153201033.png)

```
<!--lombok-->
<dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok</artifactId>
</dependency>
```

- @Data 自动生成参数的get、set方法
- @ToString 
- @AllArgsConstructor  全参构造器（如果需要不是全部的，需要自己添加）
- @NoArgsConstructor  无参构造器
- @EqualsAndHashCode
- @Slf4j  日志系统



## .yaml文件

- 基本语法：

![image-20210221113653665](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210221113653665.png)

- 注入 

![image-20210221115346866](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210221115346866.png)

## 静态资源

![image-20210222111254034](C:\Users\18622\AppData\Roaming\Typora\typora-user-images\image-20210222111254034.png)

## 首页

***.html文件，放到 `src/main/rescources/resources`下，或者`src/main/rescources/static`、`src/main/rescources/template`

## 导入已有模板

### 目录逻辑

- 所有.css、.js、图片、fonts，都放到 `resouces\static\`下

- .html文件，放到`resouces\templates\`下

## thymeleaf

### 必要步骤

引入文件头

## 各种报错

- IDEA_pom.xml_spring-boot-maven-plugin报红

  ```
  				<groupId>org.springframework.boot</groupId>
  				<artifactId>spring-boot-maven-plugin</artifactId>
  				<version>2.1.4.RELEASE</version>  //这行不能少
  ```

- 



