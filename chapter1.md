# Understanding the Basics of RxAndroid

before we start writing our reactive app lets first understand the basics of Rx and what it is?

Rx is about reacting to results. It might be an item that originated from some source. It can also be an error. Rx provides a framework to handle these items in a reactive way and to create complicated manipulation on data. for example if we want to wait for an arrival of an item, or combine multipule sources into one stream of data \(network and local for example\).

To achieve all that Rx use these core components:

* **Observables**
* **Subscription**
* **Schedulers**

first will cover observables the source of all data and the core that we will working with and what their connection to Subscription and \(Disposables\)

Furthermore, we will describe the life cycle of Observable and what's happenin to an object that travel throw the Observable stream. we will briefly go throw the **Flowable **- the big brother of the observable that know's how to handle big amount of data with high rates of publishing.

## Observables - Rivers

The stream starts with an **Observable**. must of the time it will be your best friends as long as we speak on data streams. we can have many Observables that combine to one, Basiclly, it's a universal interface to create data streams in a reactive way of programming.

The easiest why to start using Rx with existing data is use the just\(\) method and wrap that data like so:

```
Observable.just("Honda", "Yamaha")
```

This observable will not emitting data right now because we don't call the .subscribe\(\) method to it.

#### Hot and Cold Winds

Cold Observables are the most used Observable type. For example, one can be created with the following code:

```
Observable.just("Honda","Yamaha")
    .subscribe()
```

Cold Observable means that nothing is going to be emitted until there is a Subscriber to it.

A Hot Observable is an Observable that will begin emitting items internally as soon as it is created.For example if we had a status update it doesn't meter if there is something that is ready to recieve them. if there were no subscription the data will be lost.

## Disposables

Because we live in the Android platform we cannot allow the Observables live forever due to memory leaks. that's why we need to control the Observable lifecycle, to do this is very easy.

```
Disposable disposable = Observable.just("Honda", "Yamaha")
   .subscribe()
```

the only 2 methods we have in this interface are:

* dispose\(\) - action to dispose the Observable.
* isDisposed - return an Boolean if the Observable disposed or not.

if we want to group many Observables to a disposable we should the CompositeDisposable like so:

```
 Disposable disposable = new CompositeDisposable(
    Observable.just("Honda", "Yamaha").subscribe(),
    Observable.just("BMW", "KTM"))
```

must of the time we will want to dispose on the **onDestroy** method of the **Activity**.

## Schedulers

What Schedulers means is where the code will actually be executed and usually and on what Thread. most of the cases Subscribers are used to executing long-running tasks on the some background thread so that it wouldn't block the main UI Thread. this is wspecially relevant on Android platform where we need to compute some data or wait on the **IO Thread** and show the results on the **MainThread**.

The must used Schedulers are:

Background operations: **Schedulers.io\(\)**

MainThread operations: **AndroidSchedulers.mainThread\(\)**

```
Observable.just("Honda", "Yamaha")
    .subscribeOn(Schedulers.io())   -> will compute on the io thread
    .observeOn(AndroidSchedulers.mainThread())   -> return the result on the main thread
```

**Hardcore Tip**: we can use the compose\(\) method to create a pre created Transformer avoid write the subscribeOn and observeOn method we can use this transformer like so:

```
public static <T> Observable.Transformer<T, T> applyIOSchedulers() {
    return observable -> observable
                .subscribeOn(Schedulers.io())                   -> configure the io thread
                .observeOn(AndroidSchedulers.mainThread());     -> configure the main thread 
}
```

and this is how we is it:

```
 Observable.from(data)
                .compose(YourTransformersClass.applyIOSchedulers())
                ....
```

now we can enjoy the stream every time with Schedulers configured and save the boilerplate :\)

## Flowable

Flowable has almost the same methods as Observable but this guy know's to deal with pressure of data, what it means that it let you process items that emitted faster from the source of data. 

Assume that you have a source that can emit a million items per second. However, the nextstep uses those items to do a network request. We know, for sure, that we cannot do more than 50 requests per second: 

Clearly, the problem here is that the available memory will be exhausted and the programming will fail with an **OutOfMemory \(OOM\)** exception.

to avoid these situations the Observable can be converted to Flowable with to **.toFlowable\(\) **method.

```
observable.toFlowable(BackpressureStrategy.MISSING)
                .observeOn(Schedulers.io())
                .subscribe()
```

so, when to use the big brother?

* when you deal with 10k+ elements that are generated in some fashion somewhere and thus chain can tell the source to limit the amount it generates
* reading from database or from the disk

types of BackpressureStrategy:

| [`BUFFER`](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/BackpressureStrategy.html#BUFFER)Buffers_all_onNext values until the downstream consumes it. |
| :--- |


| [`DROP`](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/BackpressureStrategy.html#DROP)Drops the most recent onNext value if the downstream can't keep up. |
| :--- |
| [`ERROR`](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/BackpressureStrategy.html#ERROR)Signals a MissingBackpressureException in case the downstream can't keep up. |
| [`LATEST`](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/BackpressureStrategy.html#LATEST)Keeps only the latest onNext value, overwriting any previous value if the downstream can't keep up. |
| [`MISSING`](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/BackpressureStrategy.html#MISSING)OnNext events are written without any buffering or dropping. |

when there is a situation of dropping items we can also call the **.sample\(\). **it will emit items only periodically, and it will take only the last value that's available.

```
observable.toFlowable(BackpressureStrategy.MISSING).sample(10, TimeUnit.MILLISECONDS)
        .observeOn(Schedulers.computation()).subscribe(v -> log("s", v.toString()), this::log);
```

## Other "Stream" types

###### 



