---
layout: post
tags: [js] # add tag
img: js-head.png
---

# JS - Promise 

![Promise](https://developers.google.com/web/fundamentals/primers/promises)

> The Promise object is used for deferred and asynchronous computations

## Callbacks VS Thens

### Callback 是 Js 默认的处理异步任务的办法，但是在使用时往往伴随着一些问题

![Callback-1]({{site.baseurl}}/assets/img/15113467715113.jpg)

![Callback-1]({{site.baseurl}}/assets/img/15113467899026.jpg)

1. 怎样处理错误，在第一个图中突然发现 img 没有被成功加载，或者遇到网络错误，这时在执行强制回调时，就会产生错误，这时就需要制定一些合适的策略来解决这些问题
2. 假如一个 callback 方法结束后，还需要执行一系列 callback 方法，就会出现如第二个图中的现象，非常难以阅读和调试

### 采用 Promise 编写的代码

![Promise-1]({{site.baseurl}}/assets/img/15113468654269.jpg)

---

## Promise 的状态

Fulfilled(Resolved): 当事件成功被执行

Rejected: 事件执行失败

Pending：尚不清楚成功或者失败

settled：已经执行成功或失败

---

## Promise Timeline

![Promise-Timeline]({{site.baseurl}}/assets/img/15113470164915.jpg)


如图中所示，当一个事件已经触发后，再设置监听，监听事件是不会被调用。

但是当用 Promise 时，对已经完成的 Promise 设置一个事件被解决的数值，设置的操作会被正常执行

Promise 运行在主线程，所以如果运行一个长时间的任务，可能会阻塞浏览器渲染页面，造成卡顿。所以 Promise 仅仅是一种决定同步处理任务时会出现什么结果的技术

---

## Promise 使用

### Resolve demo

> 当执行 reslove 或 rejected 方法时，证明状态已经设置完成，并且只能执行一次，然后会进入 then 或 catch 方法

下面模拟了处理异步的一种工作方式

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Promisify setTimeout</title>
</head>
<body>
	<h1>Did the promise finish?</h1>
	<div class="completion">Not yet</div>
	<script>
		function wait(ms) {

			 return new Promise(function(resolve) {
				console.log(this);
				window.setTimeout(function() {
					resolve();
				}, ms);
			});
		};

		
		var milliseconds = 2000;
		wait(milliseconds).then(finish);

		function finish() {
			var completion = document.querySelector('.completion');
			completion.innerHTML = "Complete after " + milliseconds + "ms.";
		};
	</script>
</body>
</html>
```

### Document.Ready 状态测试

**要求：在加载图片时先加载文本**

> Document.readyState 的三种状态
> loading - document 仍然被加载
> interactive -  document 被加载完成，正在加载一些子资源，如图片、样式表和框架
> complete - 所有 document 和 子资源被加载完成

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Recreating .ready()</title>
    <style>
    body {
        margin: 0px;
    }

    .responsive {
        width: 100%;
        height: 100%;
    }

    h1,
    h3,
    div,
    p {
        margin-left: 16px;
        margin-right: 16px;
    }
    </style>
</head>

<body>
    <h1>Are you doing work when the document is ready?</h1>
    <h3>Your wrapper should resolve before the huge image finishes loading. Turn on <a href="https://developers.google.com/web/tools/chrome-devtools/profile/network-performance/network-conditions">network throttling</a> to test.</h3>
    <div class="completion">Wrapper hasn't resolved yet!</div>
    <img class="responsive" src="https://www.baidu.com/img/bd_logo1.png" height="2448" width="3264" alt="SF Market Street">

    <script>
    function ready() {

        return new Promise(function(resolve) {
            function checkState() {
                console.log(document.readyState);
                if (document.readyState !== 'loading') {
                    resolve();
                }
            }

            document.addEventListener('readystatechange', checkState);
            // 当 Document.readyState 的状态改变时，执行checksate 函数
            
            checkState();
            // 这里先调用一次 checkState，是因为可以增加对 loading 状态的处理，可以看下 console
        });
    };

    ready().then(wrapperResolved);

    function wrapperResolved() {
        var completion = document.querySelector('.completion');
        completion.innerHTML = "Resolved!";
    };
    </script>
</body>

</html>
```


### XMLHttp 结合 Promise

[XMLHTTP介绍](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest)

```JS
  function get(url) {
    return new Promise(function(resolve, reject) {
      var req = new XMLHttpRequest();
      req.open('GET', url);
      req.onload = function() {
        if (req.status === 200) {
          resolve(req.response);
        } else {
          reject(Error(req.statusText));
        }
      };
      req.onerror = function() {
        reject(Error('Network Error'));
      };
      req.send();
    });
  }

   get(URL)
    .then(function(response) {
      // TO DO
    })
    .catch(function(error) {
      // TO DO
    });
```

### Fetch 的使用 - 精简 (XMLHttp 和 Promise)

[Fetch介绍](https://davidwalsh.name/fetch)

```JS
  function get(url) {
    return fetch(url, {
        method: 'get'
    });
  }

  function getJSON(url) {
    
	return get(url)
           .then(function(response) {
                console.log(response.json);

                if(!response.ok) {
                  throw Error(response.statusText ? response.statusText : 'Unknown network error');
                }

                return response.json();
            });
  }

  getJSON(URL)
     .then(function(response) {
        // TO DO
     })
     .catch(function(error) {
        // TO DO
  });
  ```



