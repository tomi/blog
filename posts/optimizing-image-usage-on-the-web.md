# Optimizing image usage on the web

Images are important in making visually appealing web experiences, but they can cause performance and UX issues if used haphazardly. According to the HTTP Archive's [2022 Web Almanac](https://almanac.httparchive.org/en/2022/), they [are the biggest contributor](https://almanac.httparchive.org/en/2022/page-weight#content-type-and-file-formats) to the page weight across the internet. How do you use images effectively? Here is a list of things to take into account:

- Optimize the image size
- Prevent [Cumulative Layout Shift](https://web.dev/cls/)
- Lazy-load images outside the initial viewport

## Optimize the image size

Image size should be optimized so that the images and the page loads swiftly. Firstly this means serving images that are correctly sized for each device. Secondly, the image size can be optimized by using a modern image format.

### Serve correctly sized images

There's no point in serving a 2000 pixels wide image for a mobile phone which has a screen width of 400 pixels. Instead, the image size should match the device size. But how many different sizes should you have? Like usual, it depends, but you should have at least one for mobile and one for desktop users.

There are several ways to create different images versions. If you only have a few images, the easiest way is to resize them manually with a tool like [ImageMagick](https://imagemagick.org/). However, manual resizing gets cumbersome fast. Libraries like [sharp](https://www.npmjs.com/package/sharp) can help in automating the process. There are also services and CDNs like [Cloudinary](https://cloudinary.com/) that can produce correctly sized images on the fly.

Once the different image sizes are available, `<img>`'s [`sizes`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-sizes) and [`srcset`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-srcset) attributes instruct the browser which ones to use. `srcset` lists the different image versions and their widths. `sizes` tells the browser how wide the image will be when it's displayed. It does not however affect the size of the image when it's rendered, it only helps the browser to choose which image version to load.

For example the following HTML tells the following things:

```html
<img
  src="/image.png"
  srcset="/image-320.png 320w, /image-640.png 640w, /image.png 1280w"
  sizes="(min-width: 768px) 50vw, 100vw"
/>
```

- Load `image.png` if the browser doesn't support `srcset` and `sizes` attributes
- There are 3 image versions: `image-320.png` which is 320 pixels wide, `image-640.png` which is 640 pixels wide and `image.png` which is 1280 pixels wide.
- If the device width is at least 768 pixels, the rendered image width will be 50 % of the viewport width. Otherwise the rendered image is the same width as the viewport.

`srcset` and `sizes` are [supported by all modern browsers](https://caniuse.com/srcset).

Read more:

[Responsive images](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)

### Use modern image formats

Compared to the traditional image formats like JPEG and PNG, modern formats like [AVIF](https://en.wikipedia.org/wiki/AVIF) and [WebP](https://en.wikipedia.org/wiki/WebP) [compress](https://web.archive.org/web/20101004134848/http://code.google.com/intl/no/speed/webp/docs/c_study.html) [better](https://netflixtechblog.com/avif-for-next-generation-image-coding-b1d75675fe4) while still looking visually appealing. While WebP is [supported by all modern browsers](https://caniuse.com/webp), AVIF is not yet [as widely supported](https://caniuse.com/avif). The [`<picture>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture) element can be used to instruct the browser to load the correct image based on which formats it supports.

For example the following HTML falls back to a PNG image if the browser doesn't support AVIF or WebP formats:

```html
<picture>
  <source srcset="image.avif" type="image/avif" />
  <source srcset="image.webp" type="image/webp" />
  <img src="image.png" />
</picture>
```

Read more:

[Using Modern Image Formats: AVIF and WebP](https://www.smashingmagazine.com/2021/09/modern-image-formats-avif-webp/)

## Prevent Cumulative Layout Shift

[Cumulative Layout Shift](https://web.dev/cls/) is one of the [Core Web Vitals](https://web.dev/vitals/) quality signals that measure end-user experience on the web. It measures how much elements move and jump unexpectedly after they have initially been rendered to the DOM. Pages with CLS are not only annoying but can sometimes even cause unintended consequences. It also affects performance, since the browser needs to reflow and repaint the page.

When it comes to images, CLS is most commonly caused by an `<img>` element that doesn't have its dimensions known while the image is loading. The solution is to ensure the browser can allocate the correct amount of space on the page while the image is loading. This can be done in two ways:

1. Include the `width` and `height` attributes for the image. This enables modern browsers to calculate the image's default aspect ratio even before the image has been loaded. The image can still be sized with CSS.

For example the following HTML and CSS tells that the image has an aspect ratio of 1:1 and scales it to its containers width. `height: auto` is needed so the image's height is not fixed at 640 pixels:

```css
img {
  width: 100%;
  height: auto;
}
```

```html
<div class="img-container">
  <img src="/image.png" width="640" height="640" />
</div>
```

2. Reserve the required space using [CSS aspect ratio boxes](https://css-tricks.com/aspect-ratio-boxes/).

Read more:

[Optimize Cumulative Layout Shift](https://web.dev/optimize-cls)
[Jank-free page loading with media aspect ratios](https://blog.logrocket.com/jank-free-page-loading-with-media-aspect-ratios/)

## Lazy-load images outside the initial viewport

Images and other content at the top of your page should be loaded as fast as possible to minimize the [Largest Contentful Paint](https://web.dev/lcp/) (LCP), which is another one of the [Core Web Vitals](https://web.dev/vitals/) quality signal. However, deferring the loading of images that are **ensured** to be outside the viewport during startup is a great optimization. This lazy-loading of images only after they are about to enter the viewport can improve the page load time and save bandwidth.

Lazy-loading can be implemented either by using the browser's native lazy-loading functionality via `<img>`'s [`loading`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-loading) attribute or by using the JavaScript.

### Browsers' native lazy-loading

Browsers can be instructed to defer the loading of an image by setting the `<img>`'s [`loading`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-loading) attribute to a value of `lazy`. If the image is in the viewport, it is loaded immediately. Otherwise it's loaded when it reaches a certain distance from the viewport, as defined by the browser. In November 2022 this is supported by [most of the modern browsers](https://caniuse.com/loading-lazy-attr). If the browser does not support the attribute, it will ignore it and load the image immediately.

It might seem that there's no harm in setting `loading="lazy"` to all images, since the ones that are in the viewport are anyways loaded immediately. This can still degrade performance, as the browser needs to first determine if the image indeed is in the viewport, which can affect the LCP.

### Lazy-loading with JS

Using the browsers' native lazy-loading is the recommended practice. However, if you need to support older browsers and want to ensure lazy-loading works for all users, the functionality needs to be polyfilled. This can be done with the [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver) or by using a lazy-loading library. web.dev has an [excellent guide](https://web.dev/lazy-loading-images/#images-inline-intersection-observer) on how do this as well as [a list of lazy-loading libraries](https://web.dev/lazy-loading-images/#libraries).

Read more:

[Lazy-loading images](https://web.dev/lazy-loading-images)
