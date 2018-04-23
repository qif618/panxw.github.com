---
layout: post
category: "other"
title:  "线程锁-生产消费"
tags: [Thread]
---  


> 关于方法上加锁

- 当一个线程访问一个类中的同步**静态**方法时，获取的但是CLASS类本身锁，其他线程不能访问该类的其他同步静态方法  
- 当一个线程访问**同一个实例**中同步方法时，获取的但是CLASS类本身锁，其他线程不能访问该实例中其他同步方法  
-  当一个线程访问该类的**不同实例**的同步方法时，其他线程可以访问其他同步方法  
- 非同步方法都可以访问   
ddd
<!-- more -->

> 关于生产者消费者: 生产一个就取走一个

phone对象    

	class Phone{ 
	    private String name;
	    private boolean flag = true ;
	    
	    public synchronized void set(String name) throws Exception{  
	        while(!flag){  
	        	super.wait() ; //把对象锁放开给消费者线程去调用get方法
	        }  
	        System.out.println("生产一个手机");
	        this.setName(name) ;    // 设置名称  
	        Thread.sleep(300) ;  
	        flag  = false ; // 改变标志位，表示可以取走  
	        super.notify();//唤醒消费者线程去执行，但要等到生产者线程释放锁  
	    }  
	    public synchronized void get() throws Exception{  
	        while(flag){  
	        	 super.wait() ;  //把对象锁放开，让生产者线程去调用set方法,当把锁已放开，此时进入等待
	        	//要等生产者执行完，执行下方的继续取值
	        }  
	        System.out.println("取到一个手机：" +  this.getName());
	        flag  = true ;  // 改变标志位，表示可以生产  
	        super.notify(); //唤醒生产者线程去执行，但要等到消费者线程释放锁   
	    }  
	    public void setName(String name){  
	        this.name = name ;  
	    }  
	    public String getName(){  
	        return this.name ;  
	    }  
	}  

消费者   

	class Consumer implements Runnable{  
	    private Phone phone = null ;  
	    public Consumer(Phone phone){  
	        this.phone = phone ;  
	    }  
	    public void run(){  
	        for(int i=0;i<10;i++){  
	            try {
					this.phone.get() ;
				} catch (Exception e) {
					e.printStackTrace();
				}  
	        }  
	    }  
	} 

生产者  

	class Producer implements Runnable{ // 通过Runnable实现多线程  
	    private Phone phone = null ;      // 保存Info引用  
	    public Producer(Phone info){  
	        this.phone = info ;  
	    }  
	    public void run(){  
	        for(int i=0;i<10;i++){  
	                try {
						this.phone.set("手机--"+String.valueOf(i)) ;
					} catch (Exception e) {
						e.printStackTrace();
					}   
	        }  
	    }  
	}  

生产消费线程启动  

	public class ThreadCaseDemo3{  
	    public static void main(String args[]) throws Exception{  
	        Phone info = new Phone(); // 实例化Info对象  
	        Producer pro = new Producer(info) ; // 生产者  
	        Consumer con = new Consumer(info) ; // 消费者  
	        new Thread(pro).start() ;  
	        //启动了生产者线程后，再启动消费者线程  
	        Thread.sleep(500) ; 
	        new Thread(con).start() ;  
	    }  
	}   
