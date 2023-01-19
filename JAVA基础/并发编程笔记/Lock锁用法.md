Lock锁用法

```java
public class Account{
	private Long money;
    // 创建可重入锁
    private final Lock lock = new ReentrantLock();
    //getter setter construct
    
    public void comsum(){
        //....
        lock.lock(); //上锁
        try{
            //临界资源
        }catch(Exception e){
			e.printStackTrace();   
        }finally{
        	lock.unlock(); //解锁    
        }
        
    }
}
```

线程池创建

```java
public class Test{
    public static void main(String[] args){
		//创建线程池 指定线程的固定数量为3
    	ExcutorService pools = Excutors.newFixedThreadPool(3);
    	Runnable target = new MyRunnable(); //MyRunnable实现Runnable接口
    	//添加线程池任务
    	pools.submit(target); //第一次提交任务，此时会创建新线程
    	pools.submit(target); //第二次提交任务，此时会创建新线程
    	pools.submit(target); //第三次提交任务，此时会创建新线程
    	pools.submit(target); //第四次提交任务，复用之前的线程
	}
}
class MyRunnable implements Runnable{
    
    public void run(){
        //..........
    }
}
```

方式二

```java
public class Test{
    public static void main(String[] args){
		//创建线程池 指定线程的固定数量为3
    	ExcutorService pools = Excutors.newFixedThreadPool(3);
    	Callable target = new MyCallable(); //MyRunnable实现Runnable接口
    	//添加线程池任务
    	pools.submit(target); //第一次提交任务，此时会创建新线程
    	pools.submit(target); //第二次提交任务，此时会创建新线程
    	pools.submit(target); //第三次提交任务，此时会创建新线程
    	pools.submit(target); //第四次提交任务，复用之前的线程
	}
}
class MyCallable implements Callable<String>{
    
    public String call() throws Exception{
        //..........
    }
}
```

volatile关键字

