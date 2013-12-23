# Baking Soda Paste
Baking soda paste is one of the best practices when it comes to dealing with rubber stains or, well, rubber-band scrolling on iOS. This is an in-depth guide to defeat the bad habits of iOS and let your web app vibe more like a native app.

**Before we begin, you might want to check out a [simple example](http://dashersw.github.io/baking-soda-paste/simple.html) with the baking soda paste technique in the works, or a [more complex example](http://dashersw.github.io/baking-soda-paste/complex.html) with an app-like layout.** Make sure you open the examples in your iOS device.

## Introduction
This little technique lets you deal with the disgusting rubber-band scrolling on iOS devices. With Baking Soda Paste technique, web applications look more like applications than web sites. As an added bonus, the technique hides the address bar in iOS 6, leaving the precious space for the app. A third fix is for web apps in iPhone 5. Due to unknown reasons, by default they appear with black bars on top and bottom.

So, baking soda paste solves 3 problems;

1. Rubber-band scrolling in iOS Safari.
2. Hiding address bar.
3. Black bars in full screen web apps.

Another nifty bonus is inertial scroll everywhere. You know, Safari's scrolling is not like its counterpart in native apps. It's made for improving *reading* experience, some claim. But I find it restrictive. With baking soda paste technique, you get inertial scroll in your web apps for free, without going on a limb and type `-webkit-overflow-scrolling: touch` on everything.

## Ingredients
Explore `simple.html` for fixing rubber-band scrolling in basic terms. This is as simple as it gets. With a few CSS rules, you can prevent rubber-band scrolling on  overflowing elements. It doesn't apply to non-overflowing elements, though.

Explore `complex.html` for the full technique, including all three fixes. You can mix and match any as you like. It includes a header and a footer along with a content area, as seen in many applications.

Explore `complex-annotated.html` for detailed inline explanation for the full solution. It helps with understanding quirks.

## How to apply
Everybody has a different style when it comes to constructing a web app. Therefore, this technique is as sparse as possible and can be applied in parts to your solution. Just take the simple solution to rubber-banding, or mix and match parts from the annotated source.

## Breakdown
### The beginnings of a web app
The default browser width is 980px in Safari in iPhone. This is to show whole desktop pages in a very tiny fashion, albeit complete, in that screen. It's also zoomable by pinching.

A native app uses the actual device pixels, so should we. And there's no zoom so we should fix that too. In order to achieve this, use the following meta tag.

```html
<meta name="viewport" content="user-scalable=no, initial-scale=1, width=device-width" />
```

Now actually, `initial-scale=1` makes sure there's no initial zoom, `user-scalable=no` makes sure a user cannot zoom in or out, and `width=device-width` makes sure we use the actual device pixels.

Well, almost. The retina iPhone has 640 pixels in horizontal but this meta tag makes the browser think it has 320 pixels. So you just can't use 1-pixel-wide design elements. The 1px in your CSS will amount to 2 pixels on screen.

If this is a problem for you (it mostly will be), you can just use a scale value of `0.5` as in:
```html
<meta name="viewport" content="user-scalable=no, initial-scale=0.5, width=device-width" />
```

This makes sure that we really use the whole device pixels, and you can make your designers happy once again by letting them use 1-px-wide elements.

### Rubber-banding
It all began with the disgusting rubber-banding effect. Well the effect is not so disgusting, it's rather cool in fact (with things like pull-to-refresh, etc.), but you just don't want it in your web app.

We'll begin with the rubber-banding effect that are triggered by scrolling past limits in scrolling elements. You know, the ones that have a lot of contents inside. You scroll to the top, and want to scroll further (like when you want to see a pull-to-refresh icon). And when you expect a native app to just scroll the *contents*, you're pulling the whole viewport! Now that's very bad, but it's actually very easy to prevent rubber-banding in scrolling overflowing elements. An overflowing element is one whose height is smaller than its contents'. It may or may not be scrolling; and although an element doesn't have any scrollbars, it can be an overflowing element.

For example, inline elements take the dimensions of their surroundings. But a block element in an inline element may be bigger than its container. By default, browsers will display the content fully, but the outer inline element is already overflowing, like with the `overflow: visible` CSS rule. The same idea may be found in hieararchical block elements; when the inner element goes out of its container, it means the container is overflowing. You can scroll it if you apply `overflow: scroll`.

So, putting the non-scrolling overflowing elements aside for now, the scrolling elements are easily fixed.

You just apply the following rules to `html` and `body` tags:
```css
html, body {
    height: 100%;
    overflow-y: scroll;
    -webkit-overflow-scrolling: touch;
}
```
And these to your scrolling element as in:
```css
#content {
    height: 100%;
    overflow: scroll;
}
```

Really simple, right? You must have seen other nasty solutions that even try to scroll the element 1px off when you reach an end.

So open up simple.html in your iOS Safari while you enjoy a goodbye toast to rubber-banding. Now let's step back for a moment and try to understand what's going on here.

First, you set the `height` property to 100% for the `body` and the `html`. These assure that the body will fill the screen and will not scroll past beyond it. And any overflowing content will be scrolling *inside* the body. `-webkit-overflow-scrolling` is another key factor and serves a double purpose. One, it enables inertial scroll on every element and two, it just provides the rubber-banding fix.

Make sure to have a finite height on the content container as in the example, and just apply `overflow-y: scroll`. Magic.

Now don't get me wrong, I have definitely no idea why this works. But it really does.

And how about non-scrolling, non-overflowing elements? Well they are a bit more tricky and cannot be dealth with CSS only, therefore they require JavaScript. But more on that later.

### Complex example application layout
The other fixes are demonstrated in the complex example. The example features a header, a content area and a footer as found in many applications, and are fit for such layouts. You can work the fixes out for other layouts as well.

This app layout features a 60px header, a flexible-height content area and a 60px footer.

### Hiding the address bar in iOS 6
The address bar in iOS 6 is actually more friendly to developers than its cousin in iOS 7. Although iOS 6 is waning in market share, it's still out there along with 3:2 iPhone screens. So that leaves a really small canvas for our apps and that's irritating. So let's get rid of it!

Now the first thing is, we have to make sure that we're in iOS 6 and actually in a browser (and not in standalone mode where the user added the application to home screen via the Add to Home Screen button), since iOS 7 doesn't let you hide the address bar and in standalone mode there's no browser chrome.

Here's a very crude and simple OS version sniffer;
```js
if (navigator.userAgent.indexOf('iPhone OS 6') > -1) {
    document.body.classList.add('ios6');
}
```
If the user is using a version of iOS 6, we just add a class called `ios6` to the body.

For detecting the standalone status, we use:
```js
var standaloneClass = navigator.standalone ? 'standalone' : 'no-standalone';
document.body.classList.add(standaloneClass);
```

Now we have the actual state in CSS. For hiding the address bar, the `body` should be `60px` longer than the full viewport in iOS6 Safari. But we had used `100%` for `body` `height` previously! Don't panic. Just use this:

```css
body.ios6.no-standalone {
    padding-bottom: 60px;
}
```

This gives a 60px space at the bottom. Now if the user would scroll down, the address bar would disappear. But we want it to happen automatically as soon as the app loads!

So here's a pretty one-liner to append to anywhere you like.
```js
document.body.scrollIntoView();
```

Very cool, huh? Well, that additional `60px` looks really bad. We have to cover up for it!

Now assuming you have a footer at the absolute (`position: absolute`) bottom (`bottom: 0;`), you can do this:
```css
.ios6.no-standalone footer {
    bottom: -60px;
}
```

That's more like it. Now there's a gap between the content and the footer! Never mind, just push the content further down:
```css
.ios6.no-standalone #content {
    bottom: 0;
}
```

That does it. We hid the address bar!

### Standalone mode
Standalone mode is when a user adds your page to the home screen. It's possible to hide the browser chrome there, so that would feel a lot more like a native app.
First of all, you need to add the following in order to be able to hide Safari chrome in apps added to the home screen. This enables the `standalone mode`.

```html
<meta name="apple-mobile-web-app-capable" content="yes">
```
It comes with a few problems, though. First, the status bar is pitch black in iOS 7. Second, there are strange bars on top and bottom, as if you opened an old iOS app that doesn't support 4-inch displays in a 4-inch device. Weird. To get rid of the bars, we have to add a second `meta` tag.

```html
<meta name="viewport" content="user-scalable=no, initial-scale=1" media="(device-height: 568px)" />
```
This tag is only valid for 4-inch devices. The strange thing is, you have to remove `width=device-width` this time!

Now, for the status bar. For consistency between iOS 6 and 7, we prefer to have a `black-translucent` status bar in standalone mode:
```html
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```
Having any value other than `default` fixes the black status bar issue, but again, for consistency, let's stick with `black-translucent`.

It lets you use the whole display as a canvas, just like in iOS 7. So your actual content goes beneath the status bar and is visible there. For that reason, we need to leave a space for the status bar. Since its height is 20px, we just make the header taller and push the content further down.

```css
.standalone header {
    padding-top: 20px;
    height: 80px;
}

.standalone #content {
    top: 80px;
}
```

Great!

### Rubber-band scrolling on non-scrolling elements
Now, if you want to scroll in a scrolling element, there is no rubber-banding. But you can actually pull down the viewport if you start dragging on a non-scrolling element (like the header or the footer).

Now that's also an imperfection we don't want in our app. So we need to resort to JavaScript for preventing touch moves that happen in those elements. We'll begin by listening `touchstart` events on the body. Whenever a `touchstart` occurs, we'll immediately listen for a `touchmove` event to see if we need to prevent it. Just one `touchmove` event is enough to determine this, so we'll unlisten the consequent `touchmove`s for performance reasons.

Now that we have a `touchmove` event on an element, we need to see if its as tall as its contents, or shorter. If the element is shorter than its contents, it means it should be scrolling, so we shouldn't prevent the event. Keep in mind that our backs are safe with the previous simple solution, i.e., there won't be a rubber-banding effect on scrolling elements.

But if it's not shorter, than we should prevent the `touchmove` event so that it won't trigger rubber-banding!

Keeping in mind that the DOM is a tree and there can be a lot of elements up that tree starting from the element we caught the event on, we need to traverse up the tree – up to `body` in fact (since we know that it shouldn't scroll).

That's about the whole story. Here's the code.

```js
document.body.addEventListener('touchstart', function() {
    document.body.addEventListener('touchmove', function moveListener(e) {
        document.body.removeEventListener('touchmove', moveListener);

        var el = e.target;

        do {
            if (parseInt(window.getComputedStyle(el, null).height, 10) < el.scrollHeight)
                return;
        } while (el != document.body && el.parentElement != document.body && (el = el.parentElement));

        e.preventDefault();
    });
});
```

There's one problem with this approach, though. Sometimes you are just not careful enough, and you have some elements who are shorter than their contents but shouldn't scroll, e.g. elements that act like they have `overflow: visible`. Now you can check against those elements in the code above, but I discourage it as it will have a performance impact. Plus, it prevents you from being cautious when writing CSS. Just write correct CSS; a container should, almost always, be at least as tall as its children.

Another feature of this approach – I recognize some might say it's a problem – is that it completely disables the address bar. So the user cannot scroll it back down, either to refresh the site or change the URL to navigate away. Or, use the search box for searching for something in your app.

You can enable the address bar by letting the `do-while` loop reach the `body`, i.e., by removing the `body` checks in the `while` condition. I just have no use for the address bar in an app, so I prefer it this way.

## Conclusion
So, that's all about it! With the Baking Soda Paste, you don't have to fear the rubber-banding ever again! Apply it thoroughly, rinse and repeat, and don't forget to ping me back if you like it!

## Wait! What about Android?
Sorry, I have little experience in the vast realm of browser quirks in Android. Some of the tricks here work for Android too, but I don't have any device or the will to make it work for all Android devices. But your contributions are welcome!

**OK, a quick tip about Android though.**

If you want to go retina and resort to using `0.5` for `initial-scale` property, most Android environments will just show the app on a quarter of the screen. In those cases, just add a spoonful of `target-densityDpi=320` to the meta tag and the problem will be solved. iOS ignores `target-densityDpi` setting, so you can just leave it there.
