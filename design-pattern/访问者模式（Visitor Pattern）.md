# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 访问者模式

在访问者模式（Visitor Pattern）中，我们使用了一个访问者类，它改变了元素类的执行算法。通过这种方式，元素的执行算法可以随着访问者改变而改变。这种类型的设计模式属于行为型模式。根据模式，元素对象已接受访问者对象，这样访问者对象就可以处理元素对象上的操作。


### 使用场景
主要将数据结构与数据操作分离，避免稳定的数据结构和易变的操作耦合问题。

优点： 1、符合单一职责原则。 2、优秀的扩展性。 3、灵活性。
缺点： 1、具体元素对访问者公布细节，违反了迪米特原则。 2、具体元素变更比较困难。 3、违反了依赖倒置原则，依赖了具体类，没有依赖抽象。

### 代码示例

步骤 1
定义一个表示元素的接口。

ComputerPart.java
public interface ComputerPart {
public void accept(ComputerPartVisitor computerPartVisitor);
}
步骤 2
创建扩展了上述类的实体类。

Keyboard.java
public class Keyboard  implements ComputerPart {

@Override
public void accept(ComputerPartVisitor computerPartVisitor) {
computerPartVisitor.visit(this);
}
}
Monitor.java
public class Monitor  implements ComputerPart {

@Override
public void accept(ComputerPartVisitor computerPartVisitor) {
computerPartVisitor.visit(this);
}
}
Mouse.java
public class Mouse  implements ComputerPart {

@Override
public void accept(ComputerPartVisitor computerPartVisitor) {
computerPartVisitor.visit(this);
}
}
Computer.java
public class Computer implements ComputerPart {

ComputerPart[] parts;

public Computer(){
parts = new ComputerPart[] {new Mouse(), new Keyboard(), new Monitor()};      
}


@Override
public void accept(ComputerPartVisitor computerPartVisitor) {
for (int i = 0; i < parts.length; i++) {
parts[i].accept(computerPartVisitor);
}
computerPartVisitor.visit(this);
}
}
步骤 3
定义一个表示访问者的接口。

ComputerPartVisitor.java
public interface ComputerPartVisitor {
public void visit(Computer computer);
public void visit(Mouse mouse);
public void visit(Keyboard keyboard);
public void visit(Monitor monitor);
}
步骤 4
创建实现了上述类的实体访问者。

ComputerPartDisplayVisitor.java
public class ComputerPartDisplayVisitor implements ComputerPartVisitor {

@Override
public void visit(Computer computer) {
System.out.println("Displaying Computer.");
}

@Override
public void visit(Mouse mouse) {
System.out.println("Displaying Mouse.");
}

@Override
public void visit(Keyboard keyboard) {
System.out.println("Displaying Keyboard.");
}

@Override
public void visit(Monitor monitor) {
System.out.println("Displaying Monitor.");
}
}
步骤 5
使用 ComputerPartDisplayVisitor 来显示 Computer 的组成部分。

VisitorPatternDemo.java
public class VisitorPatternDemo {
public static void main(String[] args) {

      ComputerPart computer = new Computer();
      computer.accept(new ComputerPartDisplayVisitor());
}
}
步骤 6
执行程序，输出结果：

Displaying Mouse.
Displaying Keyboard.
Displaying Monitor.
Displaying Computer.




