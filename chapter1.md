

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

#### Hot and cold

## Disposables

## Schedulers

## Flowable



