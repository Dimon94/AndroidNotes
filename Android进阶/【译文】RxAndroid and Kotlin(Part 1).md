#RxAndroid and Kotlin (Part1)
> 原文作者：Ahmed Rizwan

> 原文链接：[RxAndroid and Kotlin (Part1)](https://medium.com/@ahmedrizwan/rxandroid-and-kotlin-part-1-f0382dc26ed8#.8wixgg4a5)

> 译文作者：[Dimon](https://dimon94.github.io/)

> 译者语：由于本人水平有限，翻译可能会存在偏差，望大大们谅解。翻译文章只为交流学习，谢谢观看。

> 文中的`Subscriber`我直接译为观察者了，只是为了方便自己理解，如有不悦，见谅。

---

我开始关注[RxAndroid](https://github.com/ReactiveX/RxAndroid)已经差不多一周时间了。一开始，我并没有真正意义上地理解它...我的意思是：我抓住了它的概念，但是我却不清楚该何时使用它。不过后来看了一些例子以及一些干货文章（链接在文章末）后，我才了解了`RxAndroid`！（有了个良好的程度）当时的内心如下：

![Such Rx. Much Reactive. Wow!](https://cdn-images-1.medium.com/max/800/1*rjlr5GxQIx8o28U5nhtDxg.gif)

简而言之，你几乎可以在任何地方使用`Rx`，但你不能这样做。你应该明智地揣摩哪儿更适合使用它，因为在某种情况下，`Rx`可以提升100倍以上的生产性或者比正常命令性编程更好，而在其他情况下，则是没必要使用它的。

那么以下，我将展示`Kotlin`和`Java`这两种语言关于`Rx`的一些例子。

> 如果你现在对`Kotlin`不那么熟悉，那么我建议您可以访问以下链接:

> [Official Kotlin Website](http://kotlinlang.org/)

> [Getting Started on Android](http://kotlinlang.org/docs/tutorials/kotlin-android.html)

> [Jake Wharton’s Paper on Kotlin](https://docs.google.com/document/d/1ReS3ep-hjxWA8kZi0YqDbEhCqTt29hG8P44aA9W0DM8/edit)

> 摘要： Kotlin is an awesometacular alternative to Java, that works amazingly well on Android. And oh, it's developed by JetBrains!

> P.S. `Kotlin` 是没有分号的。:-)

---

### Rx：概念

如果你已经对于`Rx`有了很好的概念，那么你可以跳过这一part。否则...请你继续吞了下面的这颗安利！！！

那么什么是Rx呢？(⊙v⊙)蒽...其实就是反应式编程`reactive programming`...用简单的话说，反应式编程`reactive programming`其实就是一种与观察者模式`Observer Pattern`密切相关的编程模式。
其中，由被观察者`Observable`调用了观察者`Subscriber`的回调方法，就实现了由被观察者`Observable`向观察者`Subscriber`的事件传递，即观察者模式`Observer Pattern`。
![Observer Pattern](https://cdn-images-1.medium.com/max/800/1*Oa7zxVaeyF4TO6Mres4E5w.png)

`Rx`也是函数式编程的一个子集。所以经常被称为`Functional Reactive Programming`。因为...作为用户接收的数据，它们可以申请它们的转换序列。（类似于我们在Java8中的对流所做的）

![转换作为用户观察到的接收数据](https://cdn-images-1.medium.com/max/400/1*ATqZ5sek2uAPfZMdmsWHSg.png)

我们甚至可以结合或者说合并不同的流`streams`。它就是这么的灵活！所以，现在我们只记得有一堆不同的、我们(观察者`Subscriber`)可以从被观察者`Observable`中接收的数据。

现在，概念开始有点清晰，让我们回过头来看看RxJava。

在Rx中，观察者`subscriber`的三个事件回调方法：

1.`onNext(Data)` :从被观察者`Observale`中接收数据（相当于 onClick() / onEvent()）

2.`onError(Exception)` :如果抛出异常，这个方法将被调用

3.`onCompleted()` :将在事件队列完结时调用。
> `RxJava`不仅把每个事件单独处理，还会把它们看做一个队列。`RxJava` 规定，当不会再有新的 `onNext()` 发出时，需要触发 `onCompleted()` 方法作为标志。

其实这就像`Iterables`在`Java`里那样。但有所不同的是，`Iterables`是`pull`型,而Rx中的被观察者们`Observales`是`push`型，这是被观察者`Observable`将数据推到了其观察者`subscribers`中。以下是他们之间的比较表格：
![comparison table](https://cdn-images-1.medium.com/max/800/1*6xrzAdP_wa6aR80UrNxiIw.png)

> 另外需要注意的是，Rx本质上是异步的，也就是说观察者`subscribers`不会等待其他观察者`subscribers`。他们其实“异步”的过程流。

所以被观察者`Observables`推出的数据流给他们的观察者`subscribers`，然后观察者`subscribers`可以处置这些流（在上面的方法的帮助下）。我们可以通过下面的[ Marble Diagrams](http://rxmarbles.com/)来更好地了解流：
![两个不同的数据流](https://cdn-images-1.medium.com/max/800/1*2-sTf0tHsmDlent1HLIhrg.png)

这些圆圈代表着的是流上的数据对象。箭头代表该数据流动的一个方向，以有序的方式！再看看以下图：
![A mapping of a stream.](https://cdn-images-1.medium.com/max/800/1*ju5YD8bRZhdCGmptRQdmlw.png)

就像我之前提到的，我们可以将类似于map、filter、zip等等使用`operators`转变成数据（以及流）。上图表示的是一种简单的映射。所以这一转变后，观察者`subscriber`这个流将得到转变后的流，怎么样？酷吧！

我想你现在应该对Rx是如何工作已经有了一个很好的概念，所以现在让我们开始实际操作吧。

- 好，我们要做的第一件事就是打坐。(莫打趣，其实只是想思考代码)

![meditate](https://cdn-images-1.medium.com/max/800/1*I6aMRP_WrXdse197zfBh1Q.jpeg)

- 然后，创建一个被观察者`Observable`亦不是难事。

这有一些可以[创建被观察者`Observables`](https://github.com/ReactiveX/RxJava/wiki/Creating-Observables)的方式，我只是将其中三个罗列出来：

1.`Observable.from()` : 将传入的数组或 Iterable 拆分成具体对象后，依次发送出来。

```java
//Kotlin
Observable.from(listOf(1, 2, 3, 4, 5))
//Java
Observable.from(Arrays.asList(1, 2, 3, 4, 5));
//它将会发出以下这些数字 : 1 - 2 - 3 - 4 - 5
```

```java
String[] words = {"Hello", "Hi", "Aloha"};
Observable observable = Observable.from(words);
// 将会依次调用：
// onNext("Hello");
// onNext("Hi");
// onNext("Aloha");
// onCompleted();
```

2.Observable.just() :将传入的参数依次发送出来。

```java
Observable observable = Observable.just("Hello", "Hi", "Aloha");
// 将会依次调用：
// onNext("Hello");
// onNext("Hi");
// onNext("Aloha");
// onCompleted();
```

3.Observable.create() :创建一个 `Observable`，实现`OnSubscribe`接口,并告诉被观察者`observable`当它被订阅时，`OnSubscribe` 的 `call()` 方法会自动被调用，将数据发送到观察者`subscriber(s)`。

```java
//Kotlin
Observable.create(object : Observable.OnSubscribe<Int> {
    override fun call(subscriber: Subscriber<in Int>) {
        for(i in 1 .. 5)
            subscriber.onNext(i)

        subscriber.onCompleted()
    }
})
```
相同的代码的Java版本:

```java
//Java
Observable.create(new Observable.OnSubscribe<Integer>() {
    @Override
    public void call(final Subscriber<? super Integer>
subscriber) {
        for (int i = 1; i <= 5; i++)
            subscriber.onNext(i);

        subscriber.onCompleted();
    }
});
```
上面写的代码就相当于我写了`Observable.from()`的例子，但你可以看到，我们可以充分控制发出什么东西、什么时候流可以结束。还可以使用`subscribe.onError(e)`发送捕获到的异常。

好了，到了现在，我们需要实现观察者`subscriber`。

---

###实现观察者`subscriber`

在我们已经实现被`Observables`之后，我们需要的一个`subscriber`!对于安卓而言，订阅一个被`Observable`我们首先要告诉`observable`有关我们打算订阅和观察的线程。`RxAndroid`给我们的[`Schedulers`](https://github.com/ReactiveX/RxJava/wiki/The-RxJava-Android-Module)，通过它我们可以指定相应的线程。所以，让我们举一个简单的“Hello World”`observable`的例子，实现在`worker thread`完成订阅，在`main thread`上观察。

```java
//Kotlin
Observable.just("Hello World")
          .subscribeOn(Schedulers.newThread())
          //each subscription is going to be on a new thread.
          .observeOn(AndroidSchedulers.mainThread()))
          //observation on the main thread
          //Now our subscriber!
          .subscribe(object:Subscriber<String>(){
            override fun onCompleted() {
             //Completed
            }

            override fun onError(e: Throwable?) {
             //TODO : Handle error here
            }

            override fun onNext(t: String?) {
             Log.e("Output",t);
            }
           })
//Java
Observable.just("Hello World")
        .subscribeOn(Schedulers.newThread())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(new Subscriber<String>() {
            @Override
            public void onCompleted() {
                //Completion
            }

            @Override
            public void onError(final Throwable e) {
                //TODO : Handle error here
            }

            @Override
            public void onNext(final String s) {
                Log.e("Output",s);
            }
        });
```
> 你可以在[这里](https://github.com/ReactiveX/RxJava/wiki/The-RxJava-Android-Module)得到有关调度与线程的更多信息。

那，它是怎么工作的呢？

当你运行上面那段代码时，它会显示一个日志消息

```java
Output: Hello World!
```
你看，“订阅”就是如此的简单。你可以点击[这里](http://reactivex.io/documentation/operators/subscribe.html)获取更多关于订阅的相关详情。

---
###一个简单的实例：Debounce!!!
好了，你现在知道了如何创建简单的`observables`了对不对？~

那么让我们来做我们自己喜欢的`RxExamples`~~

我呢~想实现这个：

![RxExamples](https://cdn-images-1.medium.com/max/400/1*lyOcKYAvTjDnArAN4rEDNw.gif)

在这个例子里，我将文本输入到一个`EditText`中，并且针对该文本：当我输入时，一个响应将被自动触发，从而打印出我输入的文本。现在这样的反应可能被称为一个`API`的调用。所以，如果我触发此要求输入了的每一个字符——这将是一种浪费，因为我只需要知道最后一个输入，这意味着在我停止打字的时候它应该只触发一个`call`——也就是在我打字后，静止一秒的说~！

那么该如何才能在` non-reactive programming`做到这一点呢？

####Non-Reactive Solution

我用一个定时器，并且安排它在`afterTextChanged()`方法延时1000毫秒后调用`run()`方法。呃，不要忘记了也要在那添加`runOnUiThread()`。-_-

其实在概念上，实现起来并不难，但是却会让代码变得非常混乱，在`Java`中更是如此。

- `Java version`

```java
//Java
Timer timer = new Timer();

final TextView textView = (TextView) findViewById(R.id.textView);
final EditText editText = (EditText) findViewById(R.id.editText);

editText.addTextChangedListener(new TextWatcher() {
    @Override
    public void beforeTextChanged(CharSequence s, int start, int count,
                                  int after) {
    }

    @Override
    public void onTextChanged(final CharSequence s, int start, int before,
                              int count) {
        if (timer != null)
            timer.cancel();
    }

    @Override
    public void afterTextChanged(final Editable s) {
        timer = new Timer();
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        textView.setText("Output : " + editText.getText());
                    }
                });
            }

        }, 1000);
    }
});
```

- Kotlin

```
//Kotlin
var timer: Timer? = Timer()

val editTextStop = findViewById(R.id.editText) as EditText
editTextStop.addTextChangedListener(object : TextWatcher {
    override fun beforeTextChanged(s: CharSequence, start: Int, count: Int, after: Int) = Unit

    override fun onTextChanged(s: CharSequence, start: Int, before: Int, count: Int) {
            timer?.cancel()
    }

    override fun afterTextChanged(s: Editable) {
        timer = Timer()
        timer!!.schedule(object : TimerTask() {
            override fun run() {
                runOnUiThread { textView.setText("Output : " + editTextStop.getText()) }
            }
        }, 1000)
    }
})

```

####`Reactive Solution`
反应式的解决方案是一个非常自由的样板。而且只有3个步骤。

1.创建一个`observable`
2.添加`Debounce operator`，1000毫秒(1秒)的延迟
3.订阅它

- 首先是`Java`代码

```java
  Observable.create(new Observable.OnSubscribe<String>() {
                    @Override
                    public void call(final Subscriber<? super String> subscriber) {
                        editText.addTextChangedListener(new TextWatcher() {
                            @Override
                            public void beforeTextChanged(final CharSequence s, final int start, final int count, final int after) {
                            }

                            @Override
                            public void onTextChanged(final CharSequence s, final int start, final int before, final int count) {
                                subscriber.onNext(s.toString());
                            }

                            @Override
                            public void afterTextChanged(final Editable s) {
                            }
                        });
                    }
                })
                .debounce(1000, TimeUnit.MILLISECONDS)
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Action1<String>() {
                    @Override
                    public void call(final String s) {
                        textView.setText("Output : " + s);
                    }
                });
```

- `Kotlin`❤

```
 Observable.create(Observable.OnSubscribe<String> { subscriber ->
            editText.addTextChangedListener(object : TextWatcher {
                override fun afterTextChanged(s: Editable?) = Unit

                override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) = Unit

                override fun onTextChanged(s: CharSequence, start: Int, before: Int, count: Int)
                        = subscriber.onNext(s.toString())
            })
        }).debounce(1000, TimeUnit.MILLISECONDS)
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe({
                    text ->
                    textView.text = "Output : " + text
                })
```
####更少的样板——`RxBindings`!
我们可以使用[RxBindings](https://github.com/JakeWharton/RxBinding)——这是`RxJava`结合了`Android`的UI组件的`API`。它适用于`Java`和`Kotlin`！快去使用它吧！！

```
    //Java with Retrolambda and RxBinding
    RxTextView.afterTextChangeEvents(editText)
              .debounce(1000,TimeUnit.MILLISECONDS)
              .observeOn(AndroidSchedulers.mainThread())
              .subscribe(tvChangeEvent -> {
                 textView.setText("Output : " + tvChangeEvent.view()
                            .getText());
              });

    //Kotlin with RxBinding
    RxTextView.afterTextChangeEvents(editText)
              .debounce(1000, TimeUnit.MILLISECONDS)
              .observeOn(AndroidSchedulers.mainThread())
              .subscribe { tvChangeEvent ->
                        textView.text = "Output : " + tvChangeEvent.view().text
              }
```
正如你所看到的，更少的代码。如果在几个月前看着段代码，我很难在一分钟你弄清楚这是怎么回事。这可真是无价之宝！

---

下面是一些我推荐的关于Rx的一些资源！我将会继续研究`Rx+(kotlin&Java)`和继续完成`part2`的，敬请关注！

- [Official Rx Page](http://reactivex.io/)
- [Grokking RxJava Series by Dan Lew](http://blog.danlew.net/2014/09/15/grokking-rxjava-part-1/)
- [Android Rx, and Kotlin : A case study](http://beust.com/weblog/2015/03/23/android-rx-and-kotlin-a-case-study/)
- [Replace AsyncTasks with Rx](http://stablekernel.com/blog/replace-asynctask-asynctaskloader-rx-observable-rxjava-android-patterns/)
- [PhilosophicalHacker Blog on Rx](http://blog.stablekernel.com/replace-asynctask-asynctaskloader-rx-observable-rxjava-android-patterns/)
- [Implementing EventBus in Rx](http://nerds.weddingpartyapp.com/tech/2014/12/24/implementing-an-event-bus-with-rxjava-rxbus/)
- [RxKotlin](https://github.com/ReactiveX/RxKotlin)

---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!
