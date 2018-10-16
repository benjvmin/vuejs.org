---
title: Lazy Loading Images with Intersection Observer
type: cookbook
order: 14
---

## Introduction
If there was one readily available performance enhancement to help developers increase initial page loading speed, it would be successfully delaying the request of an image until it’s absolutely needed. Considering images have the ability to carry an incredible amount of bytes down the network, it doesn't make sense to ship all these images at the same time, especially if the image isn't currently required for the user! This method, called [Lazy Loading](https://developers.google.com/web/fundamentals/performance/lazy-loading-guidance/images-and-video/), defines a straightforward solution for managing the complexity of loading images on demand, while maintaining the benefits of increased loading performance for visitors of your site.


## Base Example 
Building a solution that scales well with each prospective image while accurately loading at critical moments can tough a tough task to assemble from scratch. Luckily, Vue.js provides a [Custom Directives](https://vuejs.org/v2/guide/custom-directive.html) API that primarily serves as a foundation for building low level functionality to DOM elements. With Custom Directives we can introduce dynamic Browser APIs such as [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to achieve the logic behind loading images at the correct moment in time. 

The first thing we'll do is declare a global `lazyload` directive, which can be used to target every image element as needed. Let's work from a base example and understand how this works:

```js
Vue.directive('lazyload', {
  inserted: function (el) {

    // Grab the data-url property
    let imageURL = el.dataset.src;

    // If Intersection Observer is supported 
    if ("IntersectionObserver" in window) {
      
      // Create New Intersection Observer Instance
      let observer = new IntersectionObserver(entries => {
        entries.forEach(entry => {

          // Logic for each entry passed to the callback function
          if (entry.isIntersecting) {
            el.src = imageURL;
          }

        })
      })

      observer.observe();

    } else {
      
      // Logic if Intersection Observer is not supported
    
    }
  }
})
```

## Utilizing Data Attributes

 In order to successfully prepare image elements to be lazy loaded, we need a holding place for the source URL of the image. We can utilize [data attributes](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes) by designating a specific `data-src` attribute to hold our source URL. This is ideal as the image URL will simply be stored and not be immediately fetched by the browser. We also receive the benefit of a simple Javascript API to work with when interacting with data attributes.

```html
<img data-src="url-to-your-image.jpg" alt="the-image" v-lazyload>
```


In our directive, we can conveniently grab the `data-src` property by referring to the `dataset` object in Javascript.

```js
let imageURL = el.dataset.src;
```




## Intersection Observer API
If you haven't had the chance to get acquainted with [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API), there is certainly no better time than now. For clarity we'll continually tap into Mozilla Developer Network's excellent documentation to achieve a greater understanding of Intersection Observer's makeup. To summarize:

> The Intersection Observer API provides a way to asynchronously observe changes in the intersection of a target element with an ancestor element or with a top-level document's viewport.

As a whole, Intersection Observer paves the way for developers to build incredibly performant experiences based on the relative visibility of elements. If images were naturally incredibly intelligent and lazy loaded themselves, think of Intersection Observer as the brains behind them. The good news is it's not restricted to just images, and once understood, can be easily reproduced to achieve all types of functionality- not limited to lazy loading.

### Feature Detection
Considering that Intersection Observer is a newer specification, we have to provide necessary feature detection. This requires us to wrap any of our Intersection Observer logic inside a control statement. Refer to [Can I Use](https://caniuse.com/#feat=intersectionobserver) for current support coverage.

```js
 if ("IntersectionObserver" in window) {
   // Intersection Observer Code Lives Here
 }
```


### Options Object 
Most browser APIs require a little bit of extra code from us to get started, and Intersection Observer is no different. Therefore when instantiating an Intersection Observer, it's constructor accepts two parameters. We'll start with the second parameter, which is a special “options” object that allows us to configure some primary settings for Intersection Observer. The options are as follows:

* `root` The element that is used as the viewport for checking visibility of the target. Must be the ancestor of the target. Defaults to the browser viewport if not specified or if null.

* `rootMargin` Margin around the root. Can have values similar to the CSS margin property, e.g. "10px 20px 30px 40px" (top, right, bottom, left). The values can be percentages. This set of values serves to grow or shrink each side of the root element's bounding box before computing intersections. Defaults to all zeros.

* `threshold` The element that is used as the viewport for checking visibility of the target. Must be the ancestor of the target. Defaults to the browser viewport if not specified or if null.

```js
// Default Options Object
let options = { root: null, rootMargin: "0px", threshold: 0 };
```

For the practical application of lazy loading, omitting the options object during initial setup will function as the default values, which is more or less all we need to get started.

### Observer Callback
The first parameter inside the Intersection Observer constructor is a special callback function that controls the asynchronous functionality / logic inside of our observer.
> The Intersection Observer API allows you to configure a callback that is called whenever one element, called the target, intersects either the device viewport or a specified element

When this callback is invoked, it receives an `IntersectionObserverEntry`. Each entry passed to the callback function is a containing array of objects consisting of useful properties we can interact with an act on to build amazing functionality. The properties are as follows:

* entry.boundingClientRect
* entry.intersectionRatio
* entry.intersectionRect
* entry.isIntersecting
* entry.rootBounds
* entry.target
* entry.time

The property we currently care about the most is `entry.isIntersecting`. This lets us know if our target element is intersecting with the viewport.


```js
// Create New Intersection Observer Instance
let observer = new IntersectionObserver((entries, observer) => {
  entries.forEach(entry => {

    if (entry.isIntersecting) {
      // Lazy Load Images
    }

  });
}, options);
```
 


## Building The Lazy Loading Functionality


## When To Avoid This Pattern


## Alternative Patterns
