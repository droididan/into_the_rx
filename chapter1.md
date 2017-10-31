# Understanding the Basics of RxAndroid

before we start writing our reactive app lets first understand the basics of Rx and what it is?

Rx is about reacting to results. It might be an item that originated from some source. It can also be an error. Rx provides a framework to handle these items in a reactive way and to create complicated manipulation on data. for example if we want to wait for an arrival of an item, or combine multipule sources into one stream of data \(network and local for example\).

To achieve all that Rx use these core components:

* **Observables**
* **Subscription**
* **Schedulers**

first will cover observables the source of all data and the core that we will working with and what their connection to Subscription and \(Disposables\)

Furthermore, we will describe the life cycle of Observable and what's happenin to an object that travel throw the Observable stream. we will briefly go throw the **Flowable **- the big brother of the observable that know's how to handle big amount of data with high rates of publishing.

## Observables

The stream starts with an **Observable**. must of the time it will be your best friends as long as we speak on data streams. we can have many Observables that combine to one, basiclly 

#### Hot and Cold winds

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

What Schedulers means is where the code will actually be executed and usually on what Thread.

## Flowable



