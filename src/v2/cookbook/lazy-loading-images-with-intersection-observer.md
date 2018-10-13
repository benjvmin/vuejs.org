---
title: Lazy Loading Images with Intersection Observer
type: cookbook
order: 14
---

## Introduction
If there was one readily available performance enhancement to help developers increase initial page loading speed, it would be successfully delaying the request of an image until it’s absolutely needed. Considering images have the ability to carry an incredible amount of bytes down the network, it doesn't make sense to ship all these images at the same time, especially if the image isn't currently required for the user! This method, called [Lazy Loading](https://developers.google.com/web/fundamentals/performance/lazy-loading-guidance/images-and-video/), provides a straightforward solution for managing the complexity of loading images on demand.


## Base Example 
Building a solution that scales well with each prospective image while correctly loading at critical moments can tough a tough task to assemble from scratch. Luckily, Vue.js provides a [Custom Directives](https://vuejs.org/v2/guide/custom-directive.html) API that provides a foundation for building low level functionality to DOM elements. With Custom Directives we can introduce dynamic Browser APIs such as [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to accomplish the logic behind loading the image at the correct time. By declaring a global `lazyload` directive, we can target every image element as needed. Let's work from a base example and understand how this works:

```html
<img data-url="url-to-your-image.jpg" alt="the-image" v-lazyload>
```

```js
Vue.directive('lazyload', {
  inserted: function (el) {

    // Grab the data-url property
    let imageURL = el.dataset.url;

    // If Intersection Observer is supported 
    if ("IntersectionObserver" in window) {
      
      // Create New Intersection Observer Instance
      let observer = new IntersectionObserver(entries => {
        entries.forEach(entry => {

          // Logic for each entry passed to the callback function

        });
      });

      observer.observe();
    } else {
      
      // Logic if Intersection Observer is not supported
    
    }
  }
})
```

## Working With Data Attributes

 We need to prepare any image elements to be lazy loaded before we apply our custom lazy loading logic. We can do this by storing the image url inside a data attribute, named `data-url`. We can then bind the custom `lazyload` directive to our image element. Once bound, our prepared markup should look something like this:

```html
<img data-url="url-to-your-image.jpg" alt="the-image" v-lazyload>
```

In our directive, we can conveniently grab the `data-url` property by referring to the `dataset` object in Javascript.

```js
Vue.directive('lazyload', {
  inserted: function (el) {

    let imageURL = el.dataset.url;

    // If Intersection Observer is supported 
    if ("IntersectionObserver" in window) {
      
      // Create New Intersection Observer Instance
      let observer = new IntersectionObserver(() => {
        entries.forEach(entry => {
          // Logic for each entry passed to the callback function
        });
      });

      observer.observe();
    } else {

      // Logic if Intersection Observer is not supported
    
    }

  }
})
```

## Intersection Observer API
If you haven't had the chance to get acquainted with [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API), Mozilla's documentation does an excellent job of providing a comprehensive resource to learn from. To summarize:

> The Intersection Observer API provides a way to asynchronously observe changes in the intersection of a target element with an ancestor element or with a top-level document's viewport.

This is some seriously cool stuff. As developers, the Intersection Observer API gives us granular control over the visibility of elements, in order to build our custom functionality. This is what super intelligent images would use! Knowing what Intersection Observer allows us to accomplish will also provide a better context when setting up our boilerplate.

### Feature Detection
Considering that Intersection Observer is a newer specification, we have to provide necessary feature detection. Refer to [Can I Use](https://caniuse.com/#feat=intersectionobserver) for current support coverage. This requires us to wrap any of our Intersection Observer logic inside a control statement. 

### Boilerplate 
When instantiating an Intersection Observer, it requires us to pass both a callback function and options object into the constructor.


```js
Vue.directive('lazyload', {
  inserted: function (el) {
    let imageURL = el.dataset.url;

    // If Intersection Observer is supported 
    if ("IntersectionObserver" in window) {
      
      // Default Options Object
      let options = { root: null, rootMargin: "0px", threshold: 0 };

      // Create New Intersection Observer Instance
      let observer = new IntersectionObserver(observerCallback, options);

      function observerCallback(entries, observer) {
        entries.forEach(entry => {
          // Logic for each entry passed to the callback function
        });
      }

    } else {

      // Logic if Intersection Observer is not supported
      
    }

  }
})
```



## Building The Lazy Loading Functionality


## When To Avoid This Pattern


## Alternative Patterns
