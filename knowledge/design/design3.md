### 设计模式

将会设计到第三种设计模式-行为型模式，共十一种：策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式。

![](行为型模式.png)

#### 1、策略模式

策略模式（strategy）定义了一系列算法，并将每个算法封装起来，使他们可以相互替换，且算法的变化不会影响到使用算法的客户，需要设计一个接口，为一系列实现类提供统一的方法，多个实现类实现该接口，设寄一个抽象类（可有可无，属于辅助类），提供辅助函数。

示例代码：

```java
public interface ICalculator{//统一接口
  public int calculate(String exp);
}
public abstract class AbstractCalculator{//辅助类
  public int[] split(String exp,String opt){
    String array[] = exp.split(opt);
    int arrayInt[] = new int[2];
    arrayInt[0] = Integer.parsentInt(array[0]);
    arrayInt[1] = Integer.parsentInt(array[1]);
    return arrayInt;
  }
}
public class Plus extends AbstractCalcultor implements ICalulator{
  @Override
  public int calculate(String exp){
    int arrayInt[] = split(exp,"\\+");
    return arrayInt[0] +arrayInt[1];
  }
}
public class Minus extends AbstractCalculator implements ICalculator {
 
	@Override
	public int calculate(String exp) {
		int arrayInt[] = split(exp,"-");
		return arrayInt[0]-arrayInt[1];
	}
 
}
public class Multiply extends AbstractCalculator implements ICalculator {
 
	@Override
	public int calculate(String exp) {
		int arrayInt[] = split(exp,"\\*");
		return arrayInt[0]*arrayInt[1];
	}
}
public class StrategyTest {
 
	public static void main(String[] args) {
		String exp = "2+8";
		ICalculator cal = new Plus();
		int result = cal.calculate(exp);
		System.out.println(result);
	}
}
```

策略模式的决定权在用户，系统本身提供不同算法的的实现，新增或者删除算法，对各种算法做封装。因此，策略模式多用在算法决策系统中，外部用户只需要决定使用哪个算法即可。

#### 2、模板方法模式

解释一下模板方法模式（Template Method）就是指：一个抽象类中，有一个主方法，在定义1...n个方法，可以是抽象的，也可以是实际的方法，定义一个类继承该抽象类，重写抽象方法，通过调用抽象类实现对子类的调用。

实例代码：

就是在AbstractCalculator类中定义一个主方法calculate，calculate()调用split（）等，Plus、Minus分贝继承AbstractCalculator类，通过对AbstractCalculator调用实现对子类的调用

```java
public abstract class AbstractCalculator{//抽象类
  public final int calculate(String exp,String opt){//主方法，实现对本类其他方法的调用
    int array[] = split(exp,opt);
    return calculate(array[0],array[1]);
  }
  abstract public int calculate(int num1,int num2);//被子类重写的方法
  public int[] split(String exp,String opt){
    String array[] = exp.split(opt);
    int arrayInt[] = new int[2];
    arrayInt[0] = Integer.parsentInt(array[0]);
    arrayInt[1] = Integer.parsentInt(array[1]);
    return arrayInt;
  }
}

public class Plus extends AbstractCalculator{
  @Override
  public int calculate(int num1,int num2){
    return num1 + num2;
  }
}


public class StrategyTest {
 
	public static void main(String[] args) {
		String exp = "8+8";
		AbstractCalculator cal = new Plus();
		int result = cal.calculate(exp, "\\+");
		System.out.println(result);
	}
}
```

#### 3、观察者模式

包括这个模式在内的接下来四个模式，都是类与类之间的关系，不涉及到继承，观察者模式（Observer）很好理解，尅私语邮件订阅和RSS订阅，当我们浏览一些博客或者wiki时，经常会看到RSS图标，就这的意思是，当你订阅了该文章，如有后续更新，会及时通知你，其实简单来讲一句话，当一个对象变化时，其他依赖该对象的对象都会收到通知，并且随之变化，对象之间是一种一对多的关系。

实例代码：

MySubject类时我们的主对象，Observer1和Observer2是依赖于MySubject的对象，当MySubject变化时，Observer1和Observer2必然发生变化，AbstractSubject类中定义着需要监控的对象列表，可以对其进行修改，增加或者删除被监控对象，且当MySubject变化时，负责通知列表内存在的对象。

```java
public interface Observer{//一个Observer接口
  public void update();
}
public class Observer1 implements Observer{
  @Override
  public void update(){
    System.out.println("observer1 has received!");
  }
}
public class Observer2 implements Observer {
 
	@Override
	public void update() {
		System.out.println("observer2 has received!");
	}
 
}
/**
 *Subject接口及实现类
 */
public interface Subject{
  public void add(Observer observer);//增加观察者
  public void del(Observer observer);//删除观察者
  public void notifyObservers();//通知所有的观察者
  public void operation();//自身的操作
}
public abstract class AbstractSubject implements Subject{
  private Vector<Observer> vector = new Vector<Observer>();
  @Override
	public void add(Observer observer) {
		vector.add(observer);
	}
 
	@Override
	public void del(Observer observer) {
		vector.remove(observer);
	}
	@Override 
  public void notifyObservers(){
    Enumeration<Observer> enumo = vector.elements();
    while(enumo.hasMoreElements()){
      enumo.nextElement().update();
    }
  }
}
public class MySubject extends AbstractSubject{
  @Override
  public void operation(){
    System.out.print("update self!");
    notifyObservers();
  }
}

public class ObserverTest {
 
	public static void main(String[] args) {
		Subject sub = new MySubject();
		sub.add(new Observer1());
		sub.add(new Observer2());
		
		sub.operation();
	}
 
}
```

#### 4、迭代子模式

顾名思义，迭代器模式（Iterator）就是顺序访问聚集中的对象，一般来说，集合中非常常见，如果对集合类比较熟悉的话，理解本模式十分轻松。

这句话包含两层意思：

- 一是需要遍历的对象即聚集对象
- 二是迭代器对象，用于对聚集对象进行遍历访问

实例代码：

MyCollection中定义了集合的一些操作，MyIterator中定义了一系列迭代操作，且持有Collection实例。

```java
public interface Collection{
  public Iterator iterator();
  Object get(int i);//取得集合元素
  public int size();//取得集合大小
}
public interface Iterator{
  Object previous();
  Object next();
  boolean hasNext();
  Object first();//取得第一个元素
}
public class MyCollection implements Collection{
  public String string[] = {"A","B","C","D","E"};
	@Override
	public Iterator iterator() {
		return new MyIterator(this);
	}
	@Override
	public Object get(int i) {
		return string[i];
	}
 
	@Override
	public int size() {
		return string.length;
	}

}
public class MyIterator implements Iterator{
  private Collection collection;
  private int pos = -1;
  public MyIterator(Collection collection){
    this.collcection = collection
  }
  
	@Override
	public Object previous() {
		if(pos > 0){
			pos--;
		}
		return collection.get(pos);
	}
  @Override
	public Object next() {
		if(pos<collection.size()-1){
			pos++;
		}
		return collection.get(pos);
	}
@Override
	public boolean hasNext() {
		if(pos<collection.size()-1){
			return true;
		}else{
			return false;
		}
	}
@Override
	public Object first() {
		pos = 0;
		return collection.get(pos);
	}

}
public class Test {
 
	public static void main(String[] args) {
		Collection collection = new MyCollection();
		Iterator it = collection.iterator();
		
		while(it.hasNext()){
			System.out.println(it.next());
		}
	}
}
```

#### 5、责任链模式

责任链模式（Chain of Responsibility）有多个对象，每个对象持有对下一个对象的引用，这样就会形成一条链，请求在这条链上传递，知道某一对象决定处理该请求。但是发出者并不清楚到底最终那个对象会处理该请求，所以责任链模式可以实现，再隐瞒客户端的情况下，对系统进行动态的调整。

示例代码：

Abstracthandle类提供了get和set方法，方便MyHandle类设置和修改引用对象，MyHandle是核心，实例化后生成一系列相互持有的对象，构成一条链。

```java
public interface Handler{
  public void operator();
}
public abstract class AbstractHandler {
  private Handler handler;
  public Handler getHandler(){
    return Handler;
  }
  public void setHandler(Handler handler) {
	this.handler = handler;
  }
}
public class MyHandler extends AbstractHandler implements Handler{
  private String name;
  private MyHandler(String name){
    this.name = name;
  }
  @Override
  pubic void operator(){
    System.out.println(name + "deal");
    if(getHandler()!=null){
      getHandler.operator();
    }
  }
}
public class Test {
 
	public static void main(String[] args) {
		MyHandler h1 = new MyHandler("h1");
		MyHandler h2 = new MyHandler("h2");
		MyHandler h3 = new MyHandler("h3");
 
		h1.setHandler(h2);
		h2.setHandler(h3);
 
		h1.operator();
	}
}
```

链接上的请求可以是一条链，可以是一个树，还可以是一个环，模式本身不约束这个，需要我们自己去实现，同时，在一个时刻，命令只允许有一个对象传给另一个对象，而不允许传给多个对象。

#### 6、命令模式

命令模式（Command）很好理解，举个例子，司令员下令让士兵去干件事情从整个事情的角度考虑，司令员的作用是，发出口令，口令经过传递传到了士兵耳朵里，士兵去执行，这个过程好在，三者相互解耦，任何一方都不用去依赖其他人，只需要管好自己的事情。司令员只要结果，而不管士兵如何实现。

示例代码：

Invoker是调用者（司令员），Receiver是被调用者（士兵），MyCommand是命令，实现了Command接口，持有接受对象。

```java
public interface Command{
  void exe();
}
public class MyCommand implements Command{
  private Receiver receiver;
  public MyCommand(Receiver receiver){
    this,receiver = receiver;
  }
  @Override
  public void exe(){
    receiver.action();
  }
}

pubic class Receiver{
  public void action(){
    System.out.println("command received!");
  }
}

public class Invoker{
  private Command command;
  public Invoker (Command command){
    this command = command;
  }
  public void action(){
    command.exe();
  }
}
public class Test {
 
	public static void main(String[] args) {
		Receiver receiver = new Receiver();
		Command cmd = new MyCommand(receiver);
		Invoker invoker = new Invoker(cmd);
		invoker.action();
	}
}
```

#### 7、备忘录模式

备忘录模式（Memento）主要目的是保存一个对象的某个状态,以便在适当的适合恢复对象。

假设有原始类A,A中有各种属性，A可以决定需要背负的属性，备忘录类B是用来存储A的的一些内部状态，类C呢就是一个用来存储备忘录的，且只能存储，不能修改等操作。

实例代码：

Original类时原始类，里面有需要保存的属性value及创建一个备忘录类，用来保存value值，Memento类是备忘录类，Storage类是存储备忘录的类，持有Memento

```java
public class Original{
  private String value;
  public String getValue(){
    return value;
  }
  public void setValue(String value){
    this.value = value;
  }
  public Memento createMemento(){
    return new Memento(value);
  }
  	public void restoreMemento(Memento memento){
		this.value = memento.getValue();
	}
}

public class Memento {
	
	private String value;
 
	public Memento(String value) {
		this.value = value;
	}
 
	public String getValue() {
		return value;
	}
 
	public void setValue(String value) {
		this.value = value;
	}
}
public class Storage {
	
	private Memento memento;
	
	public Storage(Memento memento) {
		this.memento = memento;
	}
 
	public Memento getMemento() {
		return memento;
	}
 
	public void setMemento(Memento memento) {
		this.memento = memento;
	}
}
public class Test {
 
	public static void main(String[] args) {
		
		// 创建原始类
		Original origi = new Original("egg");
 
		// 创建备忘录
		Storage storage = new Storage(origi.createMemento());
 
		// 修改原始类的状态
		System.out.println("初始化状态为：" + origi.getValue());
		origi.setValue("niu");
		System.out.println("修改后的状态为：" + origi.getValue());
 
		// 回复原始类的状态
		origi.restoreMemento(storage.getMemento());
		System.out.println("恢复后的状态为：" + origi.getValue());
	}
}
```

#### 8、状态模式

状态模式（State）核心思想就是：当对象的状态改变时，同时改变其行为，就拿QQ来说有几种状态，在线、隐身、忙碌等，每个状态对应不同的操作，而且你的好友也能看到你的状态。

所以状态模式就两点：

- 可以通过改变状态来获得不同的行为
- 你的好友能同时看到你的变化。

实例代码：

State类是个状态类，Context类可以实现切换。

```java

public class State {
	
	private String value;
	
	public String getValue() {
		return value;
	}
 
	public void setValue(String value) {
		this.value = value;
	}
 
	public void method1(){
		System.out.println("execute the first opt!");
	}
	
	public void method2(){
		System.out.println("execute the second opt!");
	}
}

public class Context {
 
	private State state;
 
	public Context(State state) {
		this.state = state;
	}
 
	public State getState() {
		return state;
	}
 
	public void setState(State state) {
		this.state = state;
	}
 
	public void method() {
		if (state.getValue().equals("state1")) {
			state.method1();
		} else if (state.getValue().equals("state2")) {
			state.method2();
		}
	}
}
public class Test {
 
	public static void main(String[] args) {
		
		State state = new State();
		Context context = new Context(state);
		
		//设置第一种状态
		state.setValue("state1");
		context.method();
		
		//设置第二种状态
		state.setValue("state2");
		context.method();
	}
}
```

#### 9、访问者模式

访问者模式把数据结构和作用于结构上的操作解耦合，使得操作集合可相对性自由的演化，访问者模式使用于数据结构相对稳定算法又易变化的系统。因为访问者模式使得算法操作增加变得容易，若系统数据结构对象抑郁变化，经常有新的数据对象增加进来，则不适合使用访问者模式，访问者模式的优点是增加操作很容易，因为增加操作意味着增加新的访问者。访问者模式将有关行为集中到一个访问者对象中，其改变不影响系统数据接口。其缺点是增加新的数据结构很困难。

简单来说，访问者模式（Visitor）就是一种分离对象数据结构与行为的方法，通过这种分离，可达到为一个被访问者动态的添加新的操作而无需做其他修改的效果。

实例代码：

```java
public interface Visitor{
  public void visit(Subject sub);
}
public class MyVisitor implements Visitor{
  @Override
  public void visit(Subject sub){
    System.out.println("visit the subject"+sub.getSubject());
  }
}
public interface Subject{
  public void accept(Visitor visitor);
  public String getSubject();
}
public class Mysubject implemets Subject{
  @OVerride
  public ovid accept(Visitor visitor){
    visitor.visit(this);
  }
  @Override
  public String getSubject(){
    return "love";
  }
}

public class Test {
 
	public static void main(String[] args) {
		
		Visitor visitor = new MyVisitor();
		Subject sub = new MySubject();
		sub.accept(visitor);	
	}
}
```

该模式使用场景：如果我们想为一个现有的类增加新功能，不得不考虑几个事情

1. 新功能会不会与现有的功能出现兼容性问题
2. 以后会不会在需要再添加？
3. 如果类不允许修改代码怎么办

面对这些问题，最好的解决方式就是访问者者模式，访问者模式适用于数据结构相对稳定的系统，把数据结构和算法解耦

#### 10、中介者模式

中介者模式（Mediator）也是用来降低类类之间的耦合的，如果类类之间有依赖关系的话，不利于功能的拓展和维护，因为只要修改一个对象，其他关联的对象都得进行修改，如果使用中介者模式，只需要关心和Mediator类的关系，有点像spring容器的作用。

实例代码：

```java
public interface Mediator{
  void createMediator();
  void workAll();
}

public class MyMediator implements Mediator {
 
	private User user1;
	private User user2;
	
	public User getUser1() {
		return user1;
	}
 
	public User getUser2() {
		return user2;
	}
 
	@Override
	public void createMediator() {
		user1 = new User1(this);
		user2 = new User2(this);
	}
 
	@Override
	public void workAll() {
		user1.work();
		user2.work();
	}
}

public abstract class User {
	
	private Mediator mediator;
	
	public Mediator getMediator(){
		return mediator;
	}
	
	public User(Mediator mediator) {
		this.mediator = mediator;
	}
 
	public abstract void work();
}
public class User1 extends User {
 
	public User1(Mediator mediator){
		super(mediator);
	}
	
	@Override
	public void work() {
		System.out.println("user1 exe!");
	}
}

public class User2 extends User {
 
	public User2(Mediator mediator){
		super(mediator);
	}
	
	@Override
	public void work() {
		System.out.println("user2 exe!");
	}
}

public class Test {
 
	public static void main(String[] args) {
		Mediator mediator = new MyMediator();
		mediator.createMediator();
		mediator.workAll();
	}
}
```

#### 11、解释器模式

解释器（Interpreter）主要应用在OOP的编辑器的开发中

实例代码：

```java
public interface Expression{
  public int interpret(Context context);
}

public class Plus implements Expression {
 
	@Override
	public int interpret(Context context) {
		return context.getNum1()+context.getNum2();
	}
}

public class Minus implements Expression {
 
	@Override
	public int interpret(Context context) {
		return context.getNum1()-context.getNum2();
	}
}


public class Context {
	
	private int num1;
	private int num2;
	
	public Context(int num1, int num2) {
		this.num1 = num1;
		this.num2 = num2;
	}
	
	public int getNum1() {
		return num1;
	}
	public void setNum1(int num1) {
		this.num1 = num1;
	}
	public int getNum2() {
		return num2;
	}
	public void setNum2(int num2) {
		this.num2 = num2;
	}
}

public class Test {
 
	public static void main(String[] args) {
 
		// 计算9+2-8的值
		int result = new Minus().interpret((new Context(new Plus()
				.interpret(new Context(9, 2)), 8)));
		System.out.println(result);
	}
}
```

