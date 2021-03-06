## Object类

### 1. Object类中的方法说明

 ```java
  /**
  * 1. 返回当前运行时对象的Class对象, 运行时表示：当编译时是父类Number，实际运行为Integer，则返回Integer。
  * 是一个final方法，不允许子类重写，并且也是一个native方法。
  */
  public final native Class<?> getClass(); 
  
  /**
  * 2. 该方法返回对象的哈希码，主要使用在哈希表中，比如JDK中的HashMap。
  * 通常情况下，不同的对象产生的哈希码是不同的。默认情况下，对象的哈希码是通过将该对象的内部地址转换成一个整
  * 数来实现的。
  */
  public native int hashCode();
  
  /**
  * 3. 比较两个对象是否相等。Object类的默认实现，即比较2个对象的内存地址是否相等。
  * Object类的equals方法对于任何非空引用值x和y，当x和y引用同一个对象，此方法才返回true，就是内存地址相等。
  * 如果重写了equals方法，通常有必要重写hashCode方法。
  */
  public boolean equals(Object obj);

  /**
  * 4. 创建并返回当前对象的一份拷贝。
  * 一般情况下，对于任何对象x，表达式x.clone() != x为true，x.clone().getClass()==x.getClass()也为true。
  * Object类的clone方法是一个protected的native方法。
  * Object本身没有实现Cloneable接口，不重写clone方法并且进行调用的话会发CloneNotSupportedException异常。
  */
  protected native Object clone() throws CloneNotSupportedException;
  
  /**
  * 5. Object对象的默认实现，即输出类的名字@实例的哈希码的16进制。
  * toString方法的结果应该是一个简明但易于读懂的字符串，子类应该重写这个方法。
  */
  public String toString();
  
  /**
  * 6. 唤醒一个在此对象锁（监视器）上等待的线程。
  * 如果所有的线程都在此对象上等待，那么只会选择一个线程，并且选择是任意性的。
  * 直到当前线程放弃对象上的锁之后，被唤醒的线程才可以继续处理，被唤醒的线程和其他线程将平等竞争该对象锁。
  * 因为notify只能在拥有对象监视器的所有者线程中调用，否则会抛出IllegalMonitorStateException异常。
  */
  public final native void notify();
  
  /**
  * 7. 跟notify一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。
  * 如果当前线程不是对象监视器的所有者，那么调用notifyAll同样会发生IllegalMonitorStateException异常。
  */
  public final native void notifyAll();
  
  /**
  * 8. wait方法会让当前线程等待直到另一个线程调用对象的notify或notifyAll方法，或超过参数设置的timeout超
  * 时时间。
  * 当前线程必须是此对象锁所有者，否则调用此方法会发生llegalMonitorStateException异常。
  */
  public final native void wait(long timeout) throws InterruptedException;
  
  /**
  * 9. 多了一个nanos参数，这个参数表示额外时间（以毫微秒为单位，范围是0-999999）。
  * 需要注意的是 wait(0, 0)和wait(0)效果是一样的，即一直等待。
  */
  public final void wait(long timeout, int nanos) throws InterruptedException;
  
  /**
  * 10. 跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念。
  * 一般情况下，wait方法和notify方法会一起使用的，wait方法阻塞当前线程，notify方法唤醒当前线程。
  */
  public final void wait() throws InterruptedException;
  
  /**
  * 11. 实例被垃圾回收器回收的时候触发的操作，就好比 “死前的最后一波挣扎”。
  * finalize方法是一个protected方法，Object类的默认实现是不进行任何操作。
  */
  protected void finalize() throws Throwable { }
 ```

### 2.哈希码的通用约定

- 在java程序执行过程中，在一个对象没有被改变的前提下，无论这个对象被调用多少次，hashCode方法都会返回相同的整数值。**对象的哈希码没有必要在不同的程序中保持相同的值**。
- 如果2个对象使用equals方法进行比较并且相同的话，那么这2个对象的hashCode方法的值也必须相等。
- 如果根据equals方法，得到两个对象不相等，那么这2个对象的hashCode值不需要必须不相同。但是，不相等的对象的hashCode值不同的话可以提高哈希表的性能。

### 3.什么是native方法？

- native是与C++联合开发的时候用的！**使用native关键字说明这个方法是原生函数，也就是这个方法是用C/C++语言实现的，并且被编译成了DLL，由java去调用**。
- native 是用做java 和其他语言（如c++）进行协作时使用的，也就是native 后的函数的实现不是用java写的。
- native的意思就是通知操作系统， 这个函数你必须给我实现，因为我要使用。 所以native关键字的函数都是操作系统实现的， java只能调用。
- java是跨平台的语言，既然是跨了平台，所付出的代价就是牺牲一些对底层的控制，而java要实现对底层的控制，就需要一些其他语言的帮助，这个就是native的作用了

### 4.equals方法在非空对象引用上的特性

- Reflexive，自反性。任何非空引用值x，对于x.equals(x)必须返回true。
- Symmetric，对称性。任何非空引用值x和y，如果x.equals(y)为true，那么y.equals(x)也必须为true。
- Transitive，传递性。任何非空引用值x、y和z，如果x.equals(y)为true并且y.equals(z)为true，那么x.equals(z)也必定为true。
- Consistent，一致性。任何非空引用值x和y，多次调用 x.equals(y) 始终返回 true 或始终返回 false，前提是对象上 equals 比较中所用的信息没有被修改。
- 对于任何非空引用值 x，x.equals(null) 都应返回 false。

### 5.Object类getClass()方法

```java
package test;
import java.util.Date; 
public class SuperTest extends Date{ 
    private static final long serialVersionUID = 1L; 
    private void test(){ 
       System.out.println(super.getClass().getName()); 
    } 
      
    public static void main(String[]args){ 
       new SuperTest().test(); 
    } 
}
/**
* 运行结果为：test.SuperTest
* super.getClass()是父类的getClass()方法，其父类是Date，它的getClass（）方法是继承自Object类而且没有重
* 写，所以就是调用object的getClass()方法。object的getClass()方法返回当前运行类的类名，getName()方法返回
* 结果是 包名.类名
*
*/
```