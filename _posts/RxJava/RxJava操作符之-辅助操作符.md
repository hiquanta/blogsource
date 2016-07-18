---
title: RxJava操作符之--辅助操作符
date: 2016-07-17 16:34:07
tags: RxJava
---
## delaySubscription
<!--more-->
延迟一段指定的时间再发射来自Observable的发射物
```java
Observable.just(1,2,3,4).delaySubscription(5, TimeUnit.SECONDS).subscribe(new Action1<Integer>() {

			@Override
			public void call(Integer t) {
				System.out.println(t+"");

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
1
2
3
4

```
## doOnEach
注册一个动作作为原始Observable生命周期事件的一种占位符
doOnEach操作符让你可以注册一个回调，它产生的Observable每发射一项数据就会调用它一次。你可以以Action的形式传递参数给它，这个Action接受一个onNext的变体Notification作为它的唯一参数，你也可以传递一个Observable给doOnEach，这个Observable的onNext会被调用，就好像它订阅了原始的Observable一样。
```java
Observable.just(1,2,3,4).doOnEach(new Action1<Notification<? super Integer>>() {

			@Override
			public void call(Notification<? super Integer> t) {
				System.out.println("doOnEach"+(Integer)t.getValue());
			}
		}).subscribe(new Action1<Integer>() {

			@Override
			public void call(Integer t) {
				System.out.println("OnNext"+t);
			}
		});
```
### 结果
```
doOnEach1
OnNext1
doOnEach2
OnNext2
doOnEach3
OnNext3
doOnEach4
OnNext4
doOnEachnull
```
## doOnNext
## doOnSubscribe
## doOnUnsubscribe
## doOnCompleted
## doOnError
## doOnTerminate
## finallyDo
## Materialize/Dematerialize
Materialize将数据项和事件通知都当做数据项发射，Dematerialize刚好相反。
```java
Observable.just(1, 2, 3).materialize().subscribe(i->System.out.println(i.getValue()));

```
### 结果
```
1
2
3
null
```
# ObserveOn_SubscribOn
ObserveOn:指定一个观察者在哪个调度器上观察这个Observable
SubscribOn:用来指定Observable在哪个线程上运行
```java
Observable.just(1, 2, 3).observeOn(Schedulers.newThread()).
			subscribeOn(Schedulers.newThread()).subscribe(new Action1<Integer>() {

				@Override
				public void call(Integer t) {
					System.out.println(t);

				}
			});
			 try {
					Thread.sleep(Integer.MAX_VALUE);
				} catch (InterruptedException e1) {
					e1.printStackTrace();
				}

```
### 结果
```
1
2
3


```
## Serialize
强制一个Observable连续调用并保证行为正确
## Subscribe
操作来自Observable的发射物和通知
## TimeInterval
将一个发射数据的Observable转换为发射那些数据发射时间间隔的Observable
## timeout
对原始Observable的一个镜像，如果过了一个指定的时长仍没有发射数据，它会发一个错误通知
```java
Observable.create(new Observable.OnSubscribe<Integer>() {

			@Override
			public void call(Subscriber<? super Integer> t) {
				for (int i = 0; i <= 3; i++) {
					try {
						Thread.sleep(i * 100);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					t.onNext(i);
				}
			}
		}).timeout(200, TimeUnit.MILLISECONDS)
				.subscribe(new Action1<Integer>() {

					@Override
					public void call(Integer t) {
						System.out.println(t);

					}
				});
```
## 结果
```
0
1
2
Exception in thread "RxComputationThreadPool-1" java.lang.IllegalStateException: Exception thrown on Scheduler.Worker thread. Add `onError` handling.
	at rx.internal.schedulers.ScheduledAction.run(ScheduledAction.java:60)
	at java.util.concurrent.Executors$RunnableAdapter.call(Unknown Source)
	at java.util.concurrent.FutureTask.run(Unknown Source)
	at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$201(Unknown Source)
	at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(Unknown Source)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
	at java.lang.Thread.run(Unknown Source)
Caused by: rx.exceptions.OnErrorNotImplementedException
	at rx.Observable$27.onError(Observable.java:7923)
	at rx.observers.SafeSubscriber._onError(SafeSubscriber.java:159)
	at rx.observers.SafeSubscriber.onError(SafeSubscriber.java:120)
	at rx.observers.SerializedObserver.onError(SerializedObserver.java:159)
	at rx.observers.SerializedSubscriber.onError(SerializedSubscriber.java:79)
	at rx.internal.operators.OperatorTimeoutBase$TimeoutSubscriber.onTimeout(OperatorTimeoutBase.java:162)
	at rx.internal.operators.OperatorTimeout$2$1.call(OperatorTimeout.java:53)
	at rx.internal.schedulers.ScheduledAction.run(ScheduledAction.java:55)
	... 7 more
Caused by: java.util.concurrent.TimeoutException
	... 10 more
```
## Timestamp
给Observable发射的数据项附加一个时间戳
## using
创建一个只在Observable生命周期内存在的一次性资源
## to
将Observable转换为另一个对象或数据结构
