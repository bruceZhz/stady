juc

## 含义

所谓的juc指的是java.util.concurrent包，这个包主要是用来解决java高并发下的问题，在jdk1.5版本提供。其中包含三个包，分别是java.util.concurrent(放置一些并发常用的工具类).java.util.concurrent.automic（放置一些原子类操作的类）java.util.concurrent.locks(放置一些锁)

![image-20200402113527529](C:\Users\DELL\AppData\Roaming\Typora\typora-user-images\image-20200402113527529.png)

## 锁

java并发锁位于java.util.concurrent.locks包下，该包三个接口分别是Locks，Condition，ReadWriteLock。

### Locks

> 功能

Locks是锁的接口，继承这个接口的主要实现类为ReentrantLock锁，可以实现锁的基本功能

~~~java
package com.zhanghz.test.lock;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

/**
 * test1 主要用ReentrantLock实现锁的功能
 * @author zhanghanzhang
 *
 */
public class Test1 {
	
	private ReentrantLock lock = new ReentrantLock();
	
	public static void main(String[] args) {
		Test1 test1 = new Test1();
		new Thread(()->{
			test1.call();
		},"A").start();
		
		new Thread(()->{
			test1.call();
		},"B").start();
	}
	
	public void call() {
//		线程上锁
		lock.lock();
		try {
			System.out.println("调用call方法");
			System.out.println(Thread.currentThread().getName());
			TimeUnit.SECONDS.sleep(1);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
//			线程解锁
			lock.unlock();
		}
		
	}

}

~~~



请注意：

1.Lock中的lock和unlock必须**成对出现**，否则出现死锁

2.必须保证unlock方法 执行，所以应该将其放到finally代码块中。

> 与synchronized的区别



|             synchronized             |                        Lock                         |
| :----------------------------------: | :-------------------------------------------------: |
|     内置关键字，是jvm层面的内容      |                    Java的一个类                     |
|           无法判断锁的状态           |          可以使用isLocked（）方法查询状态           |
|             一律非公平锁             |          可以在创建时传入参数指定是否公平           |
| 程序在执行完车或者抛出异常时自动释放 |             无法自动释放，需要手动指定              |
|           无法加锁一直等待           | 可以t r yLock尝试加锁，同时可以指定等待获取锁的时间 |
|              不可以中断              |                      可以中断                       |

