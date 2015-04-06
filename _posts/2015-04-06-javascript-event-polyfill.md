---
title:  "javascript 이벤트 polyfill"
date:   2015-04-06 18:01:17
categories: javascript
---

{% highlight javascript %}
var utils = {
  addListener: null,
  removeListener: null
};

if (typeof window.addEventListener === 'function') {
  utils.addListener = function (el, type, fn) {
    el.addEventListener(type, fn, false);
  };
  utils.removeListener = function (el, type, fn) {
    el.removeEventListener(type, fn, false);
  };
} else if (typeof document.attachEvent === 'function') {  // IE
  utils.addListener = function (el, type, fn) {
    el.attachEvent('on' + type, fn);
  };
  utils.removeListener = function (el, type, fn) {
    el.detachEvent('on' + type, fn);
  };
} else {  // Old Browser
  utils.addListener = function (el, type, fn) {
    el['on' + type] = fn;
  };
  utils.removeListener = function (el, type, fn) {
    el['on' + type] = null;
  };
} else 
{% endhighlight %}