---
title: RxJava操作符之--连接操作符
date: 2016-07-17 16:33:32
tags: RxJava
---
<!--more-->
## publish
将普通的Observable转换为可连接的Observable
```java
SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");
		Observable<Long> obs = Observable.interval(1, TimeUnit.SECONDS).take(5);
		//使用publish操作符将普通Observable转换为可连接的Observable
		ConnectableObservable<Long> connectableObservable = obs.publish();
		//第一个订阅者订阅，不会开始发射数据
		connectableObservable.subscribe(new Subscriber<Long>() {
		    @Override
		    public void onCompleted() {
		       System.out.println("onCompleted");
		    }
		    @Override
		    public void onError(Throwable e) {
		       System.out.println("Throwable");
		    }
		    @Override
		    public void onNext(Long along) {
		    	System.out.println( "1.onNext:"+along+"->time:"+ sdf.format(new Date()));
		    }
		});
		//开始发射数据
		System.out.println( "start time:" + sdf.format(new Date()));
		connectableObservable.connect();
		//第二个订阅者延迟2s订阅，这将导致丢失前面2s内发射的数据
		connectableObservable
		        .delaySubscription(2, TimeUnit.SECONDS)
		        .subscribe(new Subscriber<Long>() {
		    @Override
		    public void onCompleted() {
		    	System.out.println("2.onCompleted");
		    }
		    @Override
		    public void onError(Throwable e) {
		    	System.out.println( "2.onError");
		    }
		    @Override
		    public void onNext(Long along) {
		    	System.out.println( "2.onNext:"+along+"->time:"+ sdf.format(new Date()));
		    }
		});
		try {
			Thread.sleep(Integer.MAX_VALUE);
		} catch (InterruptedException e1) {
			// TODO Auto-generated catch block
			e1.printStackTrace();
		}
```
## 结果
```
start time:10:58:15
1.onNext:0->time:10:58:16
1.onNext:1->time:10:58:17
2.onNext:1->time:10:58:17
1.onNext:2->time:10:58:18
2.onNext:2->time:10:58:18
1.onNext:3->time:10:58:19
2.onNext:3->time:10:58:19
1.onNext:4->time:10:58:20
2.onNext:4->time:10:58:20
onCompleted
2.onCompleted
```
## refCount
让一个可连接的Observable行为像普通的Observable
```java
SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");

Observable<Long> obs = Observable.interval(1, TimeUnit.SECONDS).take(4);
//使用publish操作符将普通Observable转换为可连接的Observable
ConnectableObservable<Long> connectableObservable = obs.publish();
//refCount：将ConnectableObservable转化为普通Observable
Observable obsRefCount = connectableObservable.refCount();

obs.subscribe(new Subscriber<Long>() {
    @Override
    public void onCompleted() {
      System.out.println( "普通obs1：onCompleted");
    }
    @Override
    public void onError(Throwable e) {
      System.out.println( "普通obs1：onError");
    }
    @Override
    public void onNext(Long along) {
      System.out.println( "普通obs1：onNext:"+along+"->time:"+ sdf.format(new Date()));
    }
});
obs.delaySubscription(3, TimeUnit.SECONDS)
        .subscribe(new Subscriber<Long>() {
    @Override
    public void onCompleted() {
      System.out.println( "普通obs2：onCompleted");
    }
    @Override
    public void onError(Throwable e) {
      System.out.println( "普通obs2：onError");
    }
    @Override
    public void onNext(Long along) {
      System.out.println( "普通obs2：onNext:"+along+"->time:"+ sdf.format(new Date()));
    }
});

obsRefCount.subscribe(new Subscriber<Long>() {
    @Override
    public void onCompleted() {
      System.out.println( "obsRefCount1：onCompleted");
    }
    @Override
    public void onError(Throwable e) {
      System.out.println( "obsRefCount1：onError");
    }
    @Override
    public void onNext(Long along) {
      System.out.println( "obsRefCount1：onNext:"+along+"->time:"+ sdf.format(new Date()));
    }
});
obsRefCount.delaySubscription(3, TimeUnit.SECONDS)
        .subscribe(new Subscriber<Long>() {
            @Override
            public void onCompleted() {
              System.out.println( "obsRefCount2：onCompleted");
            }
            @Override
            public void onError(Throwable e) {
              System.out.println( "obsRefCount2：onError");
            }
            @Override
            public void onNext(Long along) {
              System.out.println( "obsRefCount2：onNext:"+along+"->time:"+ sdf.format(new Date()));
            }
        });
try {
  Thread.sleep(Integer.MAX_VALUE);
} catch (InterruptedException e1) {
  // TODO Auto-generated catch block
  e1.printStackTrace();
}
```
## 结果
```
普通obs1：onNext:0->time:10:59:44
obsRefCount1：onNext:0->time:10:59:44
普通obs1：onNext:1->time:10:59:45
obsRefCount1：onNext:1->time:10:59:45
普通obs1：onNext:2->time:10:59:46
obsRefCount1：onNext:2->time:10:59:46
obsRefCount2：onNext:2->time:10:59:46
普通obs1：onNext:3->time:10:59:47
普通obs1：onCompleted
普通obs2：onNext:0->time:10:59:47
obsRefCount1：onNext:3->time:10:59:47
obsRefCount2：onNext:3->time:10:59:47
obsRefCount1：onCompleted
obsRefCount2：onCompleted
普通obs2：onNext:1->time:10:59:48
普通obs2：onNext:2->time:10:59:49
普通obs2：onNext:3->time:10:59:50
普通obs2：onCompleted

```
## replay
```java
SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");
		System.out.println( "start time:" + sdf.format(new Date()));

		//没有缓存的情况
		ConnectableObservable<Long> obs = Observable.interval(1, TimeUnit.SECONDS)
		        .take(5)
		        .publish();
		obs.connect();  //开始发射数据
		obs.delaySubscription(3, TimeUnit.SECONDS)
		        .subscribe(aLong -> System.out.println( "onNext:"+aLong+"->time:"+ sdf.format(new Date())));


		//缓存一个数据
		ConnectableObservable<Long> obs1 = Observable.interval(1, TimeUnit.SECONDS)
		        .take(5)
		        .replay(1);   //缓存1个数据
		obs1.connect();  //开始发射数据
		obs1.delaySubscription(3, TimeUnit.SECONDS)
		        .subscribe(aLong -> System.out.println(
		                "1.onNext:"+aLong+"->time:"+ sdf.format(new Date())));

		//缓存3s内发射的数据
		ConnectableObservable<Long> obs2 = Observable.interval(1, TimeUnit.SECONDS)
		        .take(5)
		        .replay(3, TimeUnit.SECONDS);   //缓存3s
		obs2.connect();  //开始发射数据
		obs2.delaySubscription(3, TimeUnit.SECONDS)
		        .subscribe(aLong -> System.out.println(
		                "2.onNext:"+aLong+"->time:"+ sdf.format(new Date())));
		try {
			Thread.sleep(Integer.MAX_VALUE);
		} catch (InterruptedException e1) {
			// TODO Auto-generated catch block
			e1.printStackTrace();
		}
```
### 结果
```
start time:11:28:02
1.onNext:1->time:11:28:06
2.onNext:0->time:11:28:06
2.onNext:1->time:11:28:06
2.onNext:2->time:11:28:06
1.onNext:2->time:11:28:06
onNext:3->time:11:28:06
2.onNext:3->time:11:28:07
1.onNext:3->time:11:28:07
onNext:4->time:11:28:07
1.onNext:4->time:11:28:08
2.onNext:4->time:11:28:08

```
