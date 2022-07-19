# button
**Web**

### Description

Apparently one of these buttons isn't useless...  
http://button.chal.imaginaryctf.org

### Solution 

Probably one of my favorite challs... (By that I mean it took me 2 min to solve but 2 days to realize what went on :/)

\<pic here\>  
First impression: The page is (a bunch of) white (buttons). Naturally I thought one of them pointed to the real flag. After some search and replace, however, it was not the case. 

Two days later, I realized there can be functions written but never called. `motSusfunclion` caught my eye. 
```js
var list = [];
for (let i in window) {
    if (typeof(window[i]) === "function")
        list.push(i);
}
console.log(list)
> (48) ['alert', 'atob', 'blur', 'btoa', 'cancelAnimationFrame', 'cancelIdleCallback', 'captureEvents', 'clearInterval', 'clearTimeout', 'close', 'confirm', 'createImageBitmap', 'fetch', 'find', 'focus', 'getComputedStyle', 'getSelection', 'matchMedia', 'moveBy', 'moveTo', 'open', 'postMessage', 'print', 'prompt', 'queueMicrotask', 'releaseEvents', 'reportError', 'requestAnimationFrame', 'requestIdleCallback', 'resizeBy', 'resizeTo', 'scroll', 'scrollBy', 'scrollTo', 'setInterval', 'setTimeout', 'stop', 'structuredClone', 'webkitCancelAnimationFrame', 'webkitRequestAnimationFrame', 'openDatabase', 'webkitRequestFileSystem', 'webkitResolveLocalFileSystemURL', 'notSusFunction', 'motSusfunclion', 'addEventListener', 'dispatchEvent', 'removeEventListener']
```

[Since I am not a js expert, credit to google search and stackoverflow]

Calling the function in the browser console gives me the flag. 