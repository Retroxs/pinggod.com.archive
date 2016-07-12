title: Lazy loading images on the web
date: 2016-02-17 12:00:04
desc: introduction of three methods to lazy loading images on the web
from: http://developer.telerik.com/featured/lazy-loading-images-on-the-web/
---

Lazy loading images on the web means only download images which are in or near the viewport, and don't load any other images that visitors will not likely see. To accomplish this effect, we just need a little bit of JavaScript.

At its most basic implementation always need to do:

- Build HTML with special data attribute
- Watch changes to the viewport or scrolling
- Swap the data attribute to src attribute

<!-- more -->

## Plain JavaScript

The first step is creating HTML element with data attribute:

```html
<img data-src="path/to/source" alt="">
```

Second, using `getBoundingClientRect()` to test whether an image is within the viewport:

```js
function isElementInViewport (el) {
    var rect = el.getBoundingClientRect();
    return (
        rect.top >= 0 &&
        rect.left >= 0 &&
        rect.bottom <= (window.innerHeight || document.documentElement.clientHeight) &&
        rect.right <= (window.innerWidth || document.documentElement.clientWidth)
    );
}
```

Last, geting all of images that we want to lazy load and swap the `data-src` value to `src` value:

```js
//these handlers will be removed once the images have loaded
window.addEventListener("DOMContentLoaded", lazyLoadImages);
window.addEventListener("load", lazyLoadImages);
window.addEventListener("resize", lazyLoadImages);
window.addEventListener("scroll", lazyLoadImages);

function lazyLoadImages() {
    var images = document.querySelectorAll("#main-wrapper img[data-src]"),
        item;

    // load images that have entered the viewport
    [].forEach.call(images, function (item) {
        if (isElementInViewport(item)) {
            item.setAttribute("src",item.getAttribute("data-src"));
            item.removeAttribute("data-src")
        }
    })

    // if all the images are loaded, stop calling the handler
    if (images.length == 0) {
        window.removeEventListener("DOMContentLoaded", lazyLoadImages);
        window.removeEventListener("load", lazyLoadImages);
        window.removeEventListener("resize", lazyLoadImages);
        window.removeEventListener("scroll", lazyLoadImages);
    }
}
```

## Plugin && Library

The following is some jquery plugins or libraries that can do similarly works:

- [https://github.com/tuupola/jquery_lazyload](https://github.com/tuupola/jquery_lazyload)
- [http://dinbror.dk/blazy/](http://dinbror.dk/blazy/)
- [http://luis-almeida.github.io/unveil/](http://luis-almeida.github.io/unveil/)
- [https://github.com/ressio/lazy-load-xt](https://github.com/ressio/lazy-load-xt)

## Responsive images service

Why we need this? Using this service to get responsive images. For example, we can upload a huge image in backend and download appropriate image based on the device's screen size and pixel density to compresse response size.

> Before generating different dimensions images, it's better to use image compression tool to reduce file size, such like [https://tinypng.com/](https://tinypng.com/). 

























