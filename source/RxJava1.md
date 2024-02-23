---
title: RxJava基础入门系列(一)
date: 2016-03-06 16:32:25
tags:
  - rxjava
  - rxandroid
---
# 前言

RxJava应该是近两年最流行的Android技术之一了，看到越来越多的Android开发者被RxJava搞到高潮，发出RxJava真是太爽了的感（shen）叹（yin）时，我自然也很想去感受一番~
<!-- more -->
RxJava的响应式编程思想对于习惯了使用命令式编程语言的Android开发者来说确实有点难以入门，我也是翻来覆去断断续续的鼓捣了一个多月，终于达到了入门境界，可以窥探她里面美丽的风景，发现，真的很美~

# 还是前言

额，这一系列的文章是我从一个小白到入门的经验记录，适合想入门的RxJava小白同学阅读，当然大神如果愿意花些宝贵的时间帮我指点指点，小弟感激不尽！

废话太多~接下来，我将从一个Android开发者的角度，用最直白的语言和最通俗易懂的demo来讲述我的入门过程，希望大家看过之后能够得以入门，至少能够恍然大悟，发出RxJava真的很NB的感叹~


# 储备点基本概念

### RxJava是什么？

首先呈上RxJava和RxAndroid的源码Github链接

[RxJava](https://github.com/ReactiveX/RxJava)

[RxAndroid](https://github.com/ReactiveX/RxAndroid)

额，点进去看了？看懂了？没看懂就对了，看懂了的话你可以右上角了，反正我是看不懂~以我入门级的理解，RxJava是一个实现异步操作的库，如果你不能理解，那以我的语言表达能力更通俗点更android的解释就是RxJava是一个可以让你把一些耗时阻塞主线程的操作放到独立线程中执行并且在操作执行完成后又可以通过与RxAndroid配合切回到主线程（UI线程）完成后续操作的一套东西（好吧，这其实是我目前能感觉到的RxJava对于android最有意义的地方），当然RxJava还提供了很多很好用的函数，可以让你在做一些复杂操作时爽翻天~

### 为什么是RxJava？

android能够实现异步的方式很多Thread、Handler、AsyncTask等,这些方式相信大家都用过，而且我感觉以我的渣渣表达能力在你还没有真正搞懂RxJava怎么用的时候，我解释半天你也不会明白的。。。所以
我现在能告诉你的只有一句话它们真的都没RxJava爽。


RxJava入门有点难首先一点就是要理解的概念挺多，所以不要烦，耐心把下面几点理解、理解、理解~


### 观察者模式

因为RxJava的异步是通过一种扩展的观察者模式来实现的,所以我们需要先了解观察者模式的一些基本概念。相信作为一个android开发者对于观察者模式应该不陌生，最简单的按钮点击事件OnClickListener
就是观察者模式的典型应用。通过OnClickListener给View设置一个观察者，观察View点击状态变化，
当按钮被点击自动给予观察者一个反馈去执行后续操作。对于观察者模式最经典的解释是报社和读者，通过观察者模式又被称作发布-订阅模式不难理解，读者交钱预定报社发布的报纸，当报社有新的报纸发行时会送到读者手中~


### RxJava观察者模式工具包

在RxJava的世界里，我们有四种角色：

1. Observable  （被观察者）
2. Observer    （观察者）
3. Subscriber  （订阅者）
4. Subject    （事件）

Observable和Subject是两个“生产”实体，Observer和Subscriber是两个“消费”实体。

这里Observer和Subscriber容易让人迷惑，其实Subscriber 对 Observer 接口进行了一些扩展，他们的基本使用方式一样，实质上，在 RxJava 的 subscribe（订阅） 过程中，Observer 也总是会先被转换成一个 Subscriber 再使用，所以可以简单理解为一类东西。


# OK，High起来


为了帮助你更好的理解，我决定用奥运赛场上的射击比赛的例子来讲解接下来的内容~


### 创建一个Observable

Observable即被观察者，即射击选手~

RxJava提供Observable.create()方法来创建一个观察者，创建的时候需要传入一个OnSubscribe对象。

OnSubscribe对象的作用是当Observable被订阅（即有观察者想要观察被观察者）的时候，OnSubscribe里的call()方法会被自动调用，执行方法里的操作。

用我们的方式理解就是，当裁判一声令下，射击选手就开始射击射击射击...结束射击~正如下面代码中显示的一样~


	Observable observable = Observable.create(new Observable.OnSubscribe<String>() {
	    @Override
	    public void call(Subscriber<? super String> subscriber) {
	        //开始射击
	        subscriber.onNext("第一发");
	        subscriber.onNext("第二发");
	        subscriber.onNext("第三发");
	        subscriber.onNext("第四发");
	        subscriber.onNext("第五发");
	        //完成射击
	        subscriber.onCompleted();
	    }
	});


### 创建一个Observer

Observer即观察者，即裁判~

RxJava中Observer是一个接口，有三个方法onNext，onCompleted，onError。三个方法会在观察的过程中被调用，当观察者开始观察被观察者时，首先会触发onNext方法，当观察结束时触发onCompleted方法，当观察过程中出现异常时触发onError方法。

继续用我们的例子解释，有了裁判，这时比赛开始裁判观察射击运动员的射击情况，运动员每射击一次裁判的onNext方法都会被调用一次记录，如果运动员射击的过程很顺利，则会一直调用onNext到射击完成调用onCompleted结束射击过程。但是这个过程中可能是会出现问题的~譬如运动员比赛过程中想不开，拿枪把自己给蹦了，额，这时就会触发我们的onError方法~

	Observer<String> observer = new Observer<String>() {
	    @Override
	    public void onNext(String s) {
	        Log.d("fx------", s);
	    }
	
	    @Override
	    public void onCompleted() {
	        Log.d("fx------", "射击完成");
	    }
	
	    @Override
	    public void onError(Throwable e) {
	       Log.d("fx------", e.getMessage());
	    }
	};



### 连接起来

现在我们已经有了Observable和Observer，接下来就考虑怎么把二者关联起来~ Observable类中提供了subscribe()方法，接收一个observer对象作为参数，使用这个方法我们就可以直接完成Observable和Observer的关联，实现一个最简单的RxJava链式程序~

代码形式如下：

	observable.subscribe(observer);




## 入门完成

如果以上的内容对于你都没有难度，那你离入门已经很近啦~后续的部分会讲解一些RxJava提供的更多好用的方法，以及RxJava在Android实际项目中具体使用例子~

### 我们的目标是 ：优雅的写代码，愉快的装X~



