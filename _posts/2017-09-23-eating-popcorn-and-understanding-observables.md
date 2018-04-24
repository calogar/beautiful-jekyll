---
layout: post
title: Eating popcorn and understanding Observables
subtitle: What do a circus popcorn seller and an RxJS Observable have in common?
excerpt: Imagine going to the circus. You can already see the stage where the clowns and the jugglers will appear. The show is about to start, but you feel that something very important is missing. The moment you see the popcorn seller going through the seats...
bigimg: /img/popcorn.jpg
tags: [Javascript, 'Design Pattern', Observable, Angular]
---

**Note**: This post is about the [RxJS Observables library](http://reactivex.io/), which you can find in frontend frameworks like Angular. Do not be misled!

Imagine going to the circus. You can already see the stage where the clowns and the jugglers will appear. The show is about to start, but you feel that something very important is missing. The moment you see the popcorn seller going through the seats, you immediately lift your arm and call him to treat yourself a big bucket. Everything is now perfect.

It sure is a good picture, but does this have anything to do with Observables? Observables are powerful tools used to send and obtain data in an asynchronous way, much like [Promises](https://developers.google.com/web/fundamentals/primers/promises) on steroids. My point is that **Observables and that life-save popcorn seller actually have a lot of things in common**.

### The routine of a popcorn seller

Lets analyze how a popcorn seller works:

* His manager gives him a signal. It's time to go around the public with lots of goodies to sell.
* Someone calls him. He approaches the client and sells something.
* Suddenly, he is out of popcorns. He stops attending clients and goes back.
* All the clients are attended. He just goes back and waits for the next order.

### The RxJS Observable pattern

Now it's time for the analogy:

* The popcorn seller is the Observable.
* The popcorn-thirsty clients are the Observers.
* The popcorn is the payload of data that Observers desire.

In order to use RxJS Observables in our project, the first thing we need to do is to install it as a dependency with `npm`.

```console
npm install rxjs
```

Now let's see some example code that could perfectly fit in any Javascript project.

```javascript
let observable_popcornSeller = new Observable<Popcorn>( (observer_client) => {
  // The popcorn seller gets the popcorn to sell.
  const popcorn = new Popcorn();
  // The seller gives the popcorn to the attending client.
  observer_client.next(popcorn);
  // Second round of popcorn!
  observer_client.next(popcorn);
  // Third!
  observer_client.next(popcorn);
  // The function is over. Time to close.
  observer_client.complete();  
 });

// The function is about to start. Let's be ready for the popcorn!
observable_popcornSeller.subscribe(
  onSuccess_buyPopcorn,
  onError_weDontHavePopCorn,
  onComplete_theShowIsOver
);

onSuccess_buyPopcorn(popcorn) {
  // We got our popcorn!
  console.log("Popcorn obtained");
}

onError_weDontHavePopCorn(error) {
  // "error" contains the excuse the popcorn seller gave us
  console.log("Something bad happened...");
}

onComplete_theShowIsOver() {
  // The show is now over. The popcorn seller finally earns his rest.
  console.log("The show is over");
}
```

What's going on in here? The first thing we see is that we are creating an Observable, called `observable_popcornSeller`. This Observable is created as `Observable<Popcorn>`, which means that payload of data that we want to send and receive is of type `Popcorn`. This is nothing but a class that we created to illustrate the example, and it could be anything.

The `Observable` constructor accepts a [nameless function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) with one single parameter: the Observer. But why does the Observable need the Observer (the person who desires to buy popcorn) in order to construct itself? Well, consider that in order for this to work, we need to tell the popcorn seller how to attend the clients. Which means that we need to define the **behavior** of the Observable.

In this behavior function we are doing three things:

1. **Generating or fetching the data.** Here we are just creating an empty object.
2. **Sending the new data to the Observers** with the `next` method.
3. **Nofity that the Observable has finished** its work with the `complete` method.

We could also notify the Observers about possible errors, but we will keep it simple for now. Now that we know what our Observable does, **it's time for Observers to pay attention and wait for their popcorns to be ready**. They need to `subscribe` to the Observer.

```javascript
observable_popcornSeller.subscribe(
  onSuccess_buyPopcorn,
  onError_weDontHavePopCorn,
  onComplete_theShowIsOver
);
```

As we can see, the `subscribe` method is accepting three functions, or callbacks, as parameters. With them, we define **what the observers should do when they receive the payload, some error occurs and the Observable is finally completed**.
What is really happening here is that:

|**When the Observable...**|**Then, in the Observers...**|
|Executes `next`|The first callback, `onSuccess_buyPopcorn`, is executed.|
|Detects some errors|The second callback, `onError_weDontHavePopCorn`, is executed.|
|Executes `complete`|The first callback, `onComplete_theShowIsOver` is executed.|

### Use cases

This example was kind of dumb, but Observables have a lot of common use cases. For example, the Angular framework heavily uses RxJS Observables in its implementation, like in their `HttpClient`, a service used to create HTTP requests.

When an Angular app needs to fetch data from a server, their Components `subscribe` to the requests and wait for them to be completed. When the payload of the request is finally ready, all Components that previously subscribed to it get notified, so they can obtain the data as soon as possible.


### The benefits of RxJS

Observables are nice, but it's not a new pattern. Why bother using RxJS when many other implementations exist?
**The great thing about this library is that it contains a huge variety of implementations, methods and possibilities.**

One interesting example is the `Subject` class, that has the power of both an Observable and an Observer, therefore having access to the `next`, `complete` and also the `subscribe` method.
You can check their vast and detailed [documentation](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html) to understand that this is only the tip of the iceberg. In fact, I have seen some Angular experts say that they recommend using RxJS instead of the Angular built-in event system, which in fact, is built on top of this library.
