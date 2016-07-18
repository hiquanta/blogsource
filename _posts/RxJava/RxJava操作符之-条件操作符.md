---
title: RxJava操作符之--条件操作符
date: 2016-07-17 16:33:13
tags: RxJava
---
## all
判定是否Observable发射的所有数据都满足某个条件
<!--more-->
```java
Observable.just("大", "小", "dfa", "asdf", "asdfasdf")
				.flatMap(new Func1<String, Observable<Boolean>>() {

					@Override
					public Observable<Boolean> call(String t) {
						return Observable.just(t).all(
								new Func1<String, Boolean>() {

									@Override
									public Boolean call(String t) {
										System.out.println(t + "");
										return t.equals("小");
									}
								});
					}
				}).subscribe(new Action1<Boolean>() {

					@Override
					public void call(Boolean t) {
						if (t)
							System.out.println("出现‘小’");

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
大
小
出现‘小’
dfa
asdf
asdfasdf
```
amb
给定两个或多个Observables，它只发射首先发射数据或通知的那个Observable的所有数据
```java
Observable<Integer> delay3 = Observable.just(1, 2, 3).delay(3000, TimeUnit.MILLISECONDS);
        Observable<Integer> delay2 = Observable.just(4, 5, 6).delay(1000, TimeUnit.MILLISECONDS);
        Observable<Integer> delay1 = Observable.just(7, 8, 9).delay(2000, TimeUnit.MILLISECONDS);
        Observable.amb(delay1, delay2, delay3).subscribe(new Action1<Integer>() {

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
### 结果
```
4
5
6
```
## Contains
判定一个Observable是否发射一个特定的值
```java
Observable.just("大","小","dfa","asdf","asdfasdf").contains("小")
		.subscribe(new Action1<Boolean>() {

			@Override
			public void call(Boolean t) {
				if(t)
				System.out.println("has");
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
has
```
## DefaultIfEmpty
发射来自原始Observable的值，如果原始Observable没有发射任何值，就发射一个默认值
```java
Observable.create(new Observable.OnSubscribe<Integer>() {

			@Override
			public void call(Subscriber<? super Integer> t) {
				t.onCompleted();

			}
		}).defaultIfEmpty(10).subscribe(new Action1<Integer>() {

			@Override
			public void call(Integer t) {
				System.out.println(t);
			}

		});
```
### 结果
```
10
```
## SequenceEqual
判定两个Observables是否发射相同的数据序列。
```java
Observable.sequenceEqual(Observable.just(1, 2, 3),
    Observable.just(1, 2, 3)).subscribe(new Action1<Boolean>() {

  @Override
  public void call(Boolean t) {
    System.out.println(t);
  }
});
Observable.sequenceEqual(Observable.just(1, 2, 3),
    Observable.just(1, 2, 3)).subscribe(new Action1<Boolean>() {

  @Override
  public void call(Boolean t) {
    System.out.println(t);
  }
});
```
### 结果
```
true
true
```
## skipUntil
丢弃原始Observable发射的数据，直到第二个Observable发射了一项数据
```java
Observable.interval(1, TimeUnit.SECONDS)
				.skipUntil(Observable.timer(5, TimeUnit.SECONDS))
				.subscribe(new Action1<Long>() {

					@Override
					public void call(Long t) {
						System.out.println(t);
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
4
5
6
7
8
9
10
11
12
...
```
## skipWhile
丢弃Observable发射的数据，直到一个指定的条件不成立
```java
Observable.interval(1, TimeUnit.SECONDS)
				.skipWhile(new Func1<Long, Boolean>() {

					@Override
					public Boolean call(Long t) {
						// TODO Auto-generated method stub
						return t < 6;
					}
				}).subscribe(new Action1<Long>() {

					@Override
					public void call(Long t) {
						System.out.println(t);

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
6
7
8
9
10
11
12
13
14
15
16
17
18
19
...
```
## takeUntil
当第二个Observable发射了一项数据或者终止时，丢弃原始Observable发射的任何数据

> TakeUntil订阅并开始发射原始Observable，它还监视你提供的第二个Observable。如果第二个Observable发射了一项数据或者发射了一个终止通知，TakeUntil返回的Observable会停止发射原始Observable并终止。

```java
Observable.interval(1, TimeUnit.SECONDS)
				.takeUntil(Observable.timer(10, TimeUnit.SECONDS))
				.subscribe(new Action1<Long>() {

					@Override
					public void call(Long t) {
						System.out.println(t);
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
1
2
3
4
5
6
7
8
```
## takeWhile
发射Observable发射的数据，直到一个指定的条件不成立
```java
Observable.interval(1, TimeUnit.SECONDS)
				.takeWhile(new Func1<Long, Boolean>() {

					@Override
					public Boolean call(Long t) {
						return t < 5;
					}
				}).subscribe(new Action1<Long>() {

					@Override
					public void call(Long t) {
						System.out.println(t);

					}
				});
		try {
			Thread.sleep(Integer.MAX_VALUE);
		} catch (InterruptedException e1) {
			// TODO Auto-generated catch block
			e1.printStackTrace();
		}
```
## takeWhile
发射Observable发射的数据，直到一个指定的条件不成立
```java
Observable.interval(1, TimeUnit.SECONDS)
      .takeWhile(new Func1<Long, Boolean>() {

        @Override
        public Boolean call(Long t) {
          return t < 5;
        }
      }).subscribe(new Action1<Long>() {

        @Override
        public void call(Long t) {
          System.out.println(t);

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
1
2
3
4
```  
