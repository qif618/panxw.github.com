---
layout: post
category: "other"
title:  "多线程打印中国人"
tags: [Thread]
---

> 创建一个线程，实现Runnable接口，按顺序打印ABC  

- wait() 与  notify/notifyAll()与synchronized搭配使用
- 当线程执行wait()时，会把当前的锁释放，然后让出CPU，进入等待状态。
- 当执行notify/notifyAll方法时，会唤醒一个处于等待该 对象锁 的线程
-然后继续往下执行，直到执行完退出对象锁锁住的区域（synchronized修饰的代码块）后再释放锁。  

<!-- more -->
具体代码  


	public class MyThreadPrinter2 implements Runnable {     
    
    private String name;     
    private Object prev;     
    private Object self;     
    
    private MyThreadPrinter2(String name, Object prev, Object self) {     
        this.name = name;     
        this.prev = prev;     
        this.self = self;     
    }     

    @Override    
    public void run() {     
        int count = 10;     
        while (count > 0) {     
            synchronized (prev) {     
                synchronized (self) {
                	if(name.equals("中")){
                		System.out.print(name+" "); 
                	}else{
                		System.out.print(name); 
                	}
                    count--;    
                      
                    self.notify();     
                }     
                try {     
                    prev.wait();     
                } catch (InterruptedException e) {     
                    e.printStackTrace();     
                }     
            }     
    
        }     
    }     
    
    public static void main(String[] args) throws Exception {     
        Object a = new Object();     
        Object b = new Object();     
        Object c = new Object();     
        MyThreadPrinter2 pa = new MyThreadPrinter2("中", c, a);//打印完中，唤醒需要a对象的线程pb去执行打印B，并释放c    
        MyThreadPrinter2 pb = new MyThreadPrinter2("国", a, b);//打印完国，唤醒需要b对象的线程pc去执行打印C，并释放a      
        MyThreadPrinter2 pc = new MyThreadPrinter2("人", b, c);//打印完人，唤醒需要c对象的线程pa去执行打印A，并释放b      
             
        new Thread(pa).start();  
        Thread.sleep(100);  //确保按顺序中、国、人执行  
        new Thread(pb).start();  
        Thread.sleep(100);    
        new Thread(pc).start();     
        Thread.sleep(100);    
        }     
	}    
  


