---
title: RxJava操作符之--创建操作符
date: 2016-07-17 10:41:41
tags: RxJava
---
## create
使用一个函数从头开始创建一个Observable
<!--more-->
```java
Observable.create(new Observable.OnSubscribe<Integer>() {

				@Override
				public void call(Subscriber<? super Integer> t) {
					 try {
				            if (!t.isUnsubscribed()) {
				                for (int i = 1; i < 5; i++) {
				                    t.onNext(i);
				                }
				                t.onCompleted();
				            }
				        } catch (Exception e) {
				            t.onError(e);
				        }

				}
			}).subscribe(new Subscriber<Integer>() {

				@Override
				public void onCompleted() {
					 System.out.println("Sequence complete.");
				}

				@Override
				public void onError(Throwable e) {
					 System.err.println("Error: " + e.getMessage());
				}

				@Override
				public void onNext(Integer t) {
					System.out.println("Next: " + t);
				}
			});
```
### 运行结果：
```
Next: 2
Next: 3
Next: 4
Sequence complete.
```
## defer
直到有观察者订阅时才创建Observable，并且为每个观察者创建一个新的Observable
```java
i = 10;
		Observable<Integer> justObservable = Observable.just(i);
		i = 12;
		Observable<Integer> deferObservable = Observable
				.defer(new Func0<Observable<Integer>>() {

					@Override
					public Observable<Integer> call() {
						return Observable.just(i);
					}
				});
		i=15;
		justObservable.subscribe(new Subscriber<Integer>() {

			@Override
			public void onCompleted() {

			}

			@Override
			public void onError(Throwable e) {

			}

			@Override
			public void onNext(Integer t) {
				 System.out.println("just result:" + t.toString());
			}

		} );
		deferObservable.subscribe(new Subscriber<Integer>() {

			@Override
			public void onCompleted() {

			}

			@Override
			public void onError(Throwable e) {

			}

			@Override
			public void onNext(Integer t) {
				 System.out.println("defer result:" + t.toString());
			}
		});
```
### 输出结果
```
just result:10
defer result:15
```
## empty
创建一个不发射任何数据但是正常终止的Observable
```java
Observable<Integer> emptyObservable=Observable.empty();
		emptyObservable.subscribe(new Subscriber<Integer>() {

			@Override
			public void onCompleted() {
				System.out.println("onCompleted");
			}

			@Override
			public void onError(Throwable e) {

			}

			@Override
			public void onNext(Integer t) {
				 System.out.println("empty result:" + t.toString());
			}
		});
```
### 结果
```
onCompleted
```
## never
创建一个不发射数据也不终止的Observable
```java
Observable<Integer> nerverObservable=Observable.never();
		nerverObservable.subscribe(new Subscriber<Integer>() {

			@Override
			public void onCompleted() {
				System.out.println("onCompleted");
			}

			@Override
			public void onError(Throwable e) {
				System.out.println("onError"+e);
			}

			@Override
			public void onNext(Integer t) {
				 System.out.println("empty result:" + t.toString());
			}
		});
```
### 结果
```

```
## error
创建一个不发射数据以一个错误终止的Observable
```java
Observable<Integer> errorObservable=Observable.error(new Throwable());
		errorObservable.subscribe(new Subscriber<Integer>() {

			@Override
			public void onCompleted() {
				System.out.println("onCompleted");
			}

			@Override
			public void onError(Throwable e) {
				e.printStackTrace();
			}

			@Override
			public void onNext(Integer t) {
				 System.out.println("empty result:" + t.toString());
			}
		});
```
### 结果
```java
java.lang.Throwable
	at hiquanta.rxjava.operators.create.Empty_Never_Throw.main(Empty_Never_Throw.java:49)
```
## from
将其它种类的对象和数据类型转换为Observable
```java
Integer[] items = { 0, 1, 2, 3, 4, 5 };
		Observable<Integer> myObservable = Observable.from(items);
		myObservable.subscribe(new Action1<Integer>() {

			@Override
			public void call(Integer t) {
				 System.out.println(t);
			}
		},
	    new Action1<Throwable>() {
	        @Override
	        public void call(Throwable error) {
	            System.out.println("Error encountered: " + error.getMessage());
	        }
	    },
	    new Action0() {
	        @Override
	        public void call() {
	            System.out.println("Sequence complete");
	        }
	    });
```
### 结果:
```
0
1
2
3
4
5
Sequence complete
```
## interval
创建一个按固定时间间隔发射整数序列的Observable
```java
Observable<Long> observable=Observable.interval(1, TimeUnit.SECONDS);
  observable.subscribe(new Action1<Long>() {

    @Override
    public void call(Long t) {
      System.out.println("interval:"+t);
    }
  });
  try {
    Thread.sleep(Integer.MAX_VALUE);
  } catch (InterruptedException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  }
```
### 结果
```
interval:0
interval:1
interval:2
interval:3
interval:4
interval:5
interval:6
interval:7
interval:8
...
```
## just
创建一个发射指定值的Observable
```java
Observable.just(1, 2, 3)
    .subscribe(new Subscriber<Integer>() {
  @Override
  public void onNext(Integer item) {
      System.out.println("Next: " + item);
  }

  @Override
  public void onError(Throwable error) {
      System.err.println("Error: " + error.getMessage());
  }

  @Override
  public void onCompleted() {
      System.out.println("Sequence complete.");
  }
});
```
### 结果
```
Next: 1
Next: 2
Next: 3
Sequence complete.
```
## range
创建一个发射特定整数序列的Observable
```java
Observable.range(3, 10).subscribe(new Subscriber<Integer>() {
M
			@Override
			public void onCompleted() {
				 System.out.println("Sequence complete.");

			}

			@Override
			public void onError(Throwable e) {
				 System.out.println("Sequence complete.");

			}

			@Override
			public void onNext(Integer t) {
			    System.out.println("Next:" + t.toString());

			}
		});
```
###  结果
```
Next:3
Next:4
Next:5
Next:6
Next:7
Next:8
Next:9
Next:10
Next:11
Next:12
Sequence complete.
```
## repeat
repeat操作符就是对某一个Observable重复产生多次结果,当repeat()
接收到onComplete()会触发重订阅，默认情况下运行在一个新的线程上
```java
Observable.range(1, 5).repeat(5).subscribe(new Subscriber<Integer>()
    {

    @Override
    public void onCompleted() {
    System.out.println("onCompleted");

    }

    @Override
    public void onError(Throwable e) {
    System.out.println("onError");
    }

    @Override
    public void onNext(Integer t) {
    System.out.println("onNext:"+t);
    }
    });
```
### 结果
```
onNext:1
onNext:2
onNext:3
onNext:4
onNext:5
onNext:1
onNext:2
onNext:3
onNext:4
onNext:5
onNext:1
onNext:2
onNext:3
onNext:4
onNext:5
onNext:1
onNext:2
onNext:3
onNext:4
onNext:5
onNext:1
onNext:2
onNext:3
onNext:4
onNext:5
onCompleted
```
## repeatWhen
它不是缓存和重放原始Observable的数据序列，而是有条件的重新订阅和发射原来的Observable。
将原始Observable的终止通知（完成或错误）当做一个void数据传递给一个通知处理器，它以此来决定是否要重新订阅和发射原来的Observable。这个通知处理器就像一个Observable操作符，接受一个发射void通知的Observable为输入，返回一个发射void数据（意思是，重新订阅和发射原始Observable）或者直接终止（意思是，使用repeatWhen终止发射数据）的Observable。
```java
Observable
				.range(1, 5)
				.repeatWhen(
						new Func1<Observable<? extends Void>, Observable<?>>() {
							@Override
							public Observable<?> call(
									Observable<? extends Void> observable) {
								return Observable.timer(6, TimeUnit.SECONDS);
							}
						}).subscribe(new Observer<Integer>() {
					@Override
					public void onCompleted() {
						System.out.println("onCompleted");
					}

					@Override
					public void onError(Throwable e) {
						System.out.println("onError:" + e);
					}

					@Override
					public void onNext(Integer integer) {
						System.out.println("onNext:" + integer);
					}
				});
		try {
			Thread.sleep(Integer.MAX_VALUE);
		} catch (InterruptedException e1) {
			// TODO Auto-generated catch block
			e1.printStackTrace();
		}
```
### 结果
```
onNext:1
onNext:2
onNext:3
onNext:4
onNext:5
onNext:1
onNext:2
onNext:3
onNext:4
onNext:5
onCompleted
```
## Start
非标准模块.
返回一个Observable，它发射一个类似于函数声明的值.

## timer
创建一个Observable，它在一个给定的延迟后发射一个特殊的值。
```java
Observable.timer(4, TimeUnit.SECONDS).subscribe(new Action1<Long>() {
            @Override
            public void call(Long aLong) {
                System.out.println(aLong+"");
            }
        });
		try {
			Thread.sleep(Integer.MAX_VALUE);
		} catch (InterruptedException e1) {
			// TODO Auto-generated catch block
			e1.printStackTrace();
		}
```
### 结果
```
0
```
[代码](https://github.com/hiquanta/learningRxJava/tree/master/RxJavaDemo)
