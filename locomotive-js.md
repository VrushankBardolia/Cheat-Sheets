# Locomotive JS
## 1. Add Locomotive CSS
```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/locomotive-scroll@3.5.4/dist/locomotive-scroll.css">
```
## 2. Add Locomotive CDN
```html
<script src="https://cdn.jsdelivr.net/npm/locomotive-scroll@3.5.4/dist/locomotive-scroll.js"></script>
```
## 3. Add Locomotive JS Code
> Always write your content in `<main>` tag.

Add JS Code in `script.js`
### 1. Locomotive JS
```javascript
const scroll = new LocomotiveScroll({
    el: document.querySelector('main'),
    smooth: true
});
```
### 2. Locomotive JS + GSAP + ScrollTrigger
Add this in `script.js`
```javascript
gsap.registerPlugin(ScrollTrigger);

// Using Locomotive Scroll from Locomotive https://github.com/locomotivemtl/locomotive-scroll

const locoScroll = new LocomotiveScroll({
  el: document.querySelector("main"),
  smooth: true
});
// each time Locomotive Scroll updates, tell ScrollTrigger to update too (sync positioning)
locoScroll.on("scroll", ScrollTrigger.update);

// tell ScrollTrigger to use these proxy methods for the "main" element since Locomotive Scroll is hijacking things
ScrollTrigger.scrollerProxy("main", {
  scrollTop(value) {
    return arguments.length ? locoScroll.scrollTo(value, 0, 0) : locoScroll.scroll.instance.scroll.y;
  }, // we don't have to define a scrollLeft because we're only scrolling vertically.
  getBoundingClientRect() {
    return {top: 0, left: 0, width: window.innerWidth, height: window.innerHeight};
  },
  // LocomotiveScroll handles things completely differently on mobile devices - it doesn't even transform the container at all! So to get the correct behavior and avoid jitters, we should pin things with position: fixed on mobile. We sense it by checking to see if there's a transform applied to the container (the LocomotiveScroll-controlled element).
  pinType: document.querySelector("main").style.transform ? "transform" : "fixed"
});


// each time the window updates, we should refresh ScrollTrigger and then update LocomotiveScroll. 
ScrollTrigger.addEventListener("refresh", () => locoScroll.update());

// after everything is set up, refresh() ScrollTrigger and update LocomotiveScroll because padding may have been added for pinning, etc.
ScrollTrigger.refresh();
```

## Example
### index.html
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Locomotive</title>
    <link rel="stylesheet" href="style.css">

    <!-- Locomotive Scroll CSS -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/locomotive-scroll@3.5.4/dist/locomotive-scroll.css">
</head>

<body>
    <main>
        <div id="page1">
            <div class="box"></div>
        </div>
        <div id="page2">
            <div class="image">
                <img data-scroll data-scroll-speed="1"
                    src="https://plus.unsplash.com/premium_photo-1749137444919-2bf7d6964d1d?w=600&auto=format&fit=crop&q=60&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxmZWF0dXJlZC1waG90b3MtZmVlZHwxM3x8fGVufDB8fHx8fA%3D%3D"
                    alt="">
            </div>
        </div>
        <div id="page3">
            <div class="box"></div>
        </div>
    </main>

    <!-- Locomotive Scroll CDN -->
    <script src="https://cdn.jsdelivr.net/npm/locomotive-scroll@3.5.4/dist/locomotive-scroll.js"></script>
    <!-- GSAP CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.13.0/gsap.min.js"
        integrity="sha512-NcZdtrT77bJr4STcmsGAESr06BYGE8woZdSdEgqnpyqac7sugNO+Tr4bGwGF3MsnEkGKhU2KL2xh6Ec+BqsaHA=="
        crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <!-- Scroll Trigger CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.13.0/ScrollTrigger.min.js"
        integrity="sha512-P2IDYZfqSwjcSjX0BKeNhwRUH8zRPGlgcWl5n6gBLzdi4Y5/0O4zaXrtO4K9TZK6Hn1BenYpKowuCavNandERg=="
        crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <script src="script.js"></script>
</body>

</html>
```

### script.js
```js
gsap.registerPlugin(ScrollTrigger);

const locoScroll = new LocomotiveScroll({
  el: document.querySelector("main"),
  smooth: true
});
locoScroll.on("scroll", ScrollTrigger.update);

ScrollTrigger.scrollerProxy("main", {
  scrollTop(value) {
    return arguments.length ? locoScroll.scrollTo(value, 0, 0) : locoScroll.scroll.instance.scroll.y;
  },
  getBoundingClientRect() {
    return {top: 0, left: 0, width: window.innerWidth, height: window.innerHeight};
  },
  pinType: document.querySelector("main").style.transform ? "transform" : "fixed"
});

ScrollTrigger.addEventListener("refresh", () => locoScroll.update());

ScrollTrigger.refresh();

gsap.from("#page1 .box",{
    scale:0,
    opacity:0,
    duration:2,
    delay:1
});

gsap.from("#page3 .box",{
    scale:0,
    opacity:0,
    duration:1,
    scrollTrigger:{
        trigger:"#page3 .box",
        scroller:"main",
        markers:true,
        start:"top 100%",
        end:"top 30%",
        scrub:2
    }
});
```
