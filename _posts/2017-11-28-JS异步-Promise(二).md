---
layout: post
tags: [js] # add tag
img: js-head.png
---

# Promise 错误处理方法

## 处理的等价写法

```js

// 写法一

get('example.json')
.then(resolveFunc)
.catch(rejectFunc);

// 写法二

get('example.json')
.then(resolveFunc)
.then(undefined, rejectFunc);

// 写法三
get('example.json')
.then(resolveFunc, rejectFunc)；

```

> 注意写法一中 then() 和 catch() 可以都有可能被调用
> 但写法三中 then() 和 catch() 只能被调用一个或者都不调用

## 错误处理的具体执行顺序

```js
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

			 return new Promise(function(resolve,reject) {
				console.log(this);
				window.setTimeout(function() {
					reject();
				}, ms);
			});
		};

		
		var milliseconds = 2000;
		wait(milliseconds)
		.then(finish)
		.catch(function (error) {
			console.log("第一次出现异常"); 
		})
		.then(finish)
		.catch(function (error) {
			console.log("第二次出现异常");
		})
		.then(function () {
			console.log("第一次正常执行");
		})
		.catch(function (error) {
			console.log("第三次出现异常");
		});

		function finish() {
			var completion = document.querySelector('.completion');
			completion.innerHTML = "Complete after " + milliseconds + "ms.";

			XXXX(); //随便定义的函数用于制造异常
		};
	</script>
</body>
</html>
```

---

# Promise 多次请求时 then 的执行情况

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>异步测试</title>
</head>

<body>
    <h1>Did the promise finish?</h1>
    <div class="completion">Not yet</div>
    <script>
    function getUrlData(ms) {

        return new Promise(function(resolve, reject) {
                // 模拟请求网络
                window.setTimeout(function() {
                    resolve();
                }, ms)
            })
            .then(function() {
                return URL_ARRAY;
            });
    };

    function getinfoByUrl(urlArray) {
        // var sequence = Promise.resolve();
        // urlArray.forEach(function(url) {
        //     sequence = sequence
        //                 .then(function() {
        //                     return getText(url);
        //                 })
        //                 .then(addText);
        // });

        var sequence = Promise.resolve();
        urlArray.forEach(function(url) {
                        sequence
                        .then(function() {
                            return getText(url);
                        })
                        .then(addText);
        });
    };

    // 修改页面
    function addText(info) {
        var completion = document.querySelector('.completion');
        var newDiv = document.createElement("div");
        var newContent = document.createTextNode("Hi there and greetings!" + info);
        newDiv.appendChild(newContent);
        completion.appendChild(newDiv);
    }


    // 模拟请求网络获取数据
    function getText(url) {
        return new Promise(function(resolve) {
                window.setTimeout(function() {
                    resolve();
                }, 1000);
            })
            .then(function() {
                return INFO;
            });
    }

    var URL_ARRAY = ['url1', 'url2', 'url3','url4','url5'];
    var INFO = ['Hello','I am','Dawn'];
    var milliseconds = 2000;

    getUrlData(milliseconds)
        .then(getinfoByUrl)
        .catch(function(error) {
            console.log("出现异常");
        });
    </script>
</body>

</html>
```

在两个 `for` 语句的循环下可以看到，Promise 的执行顺序分别为顺序执行和异步执行，但也带来了一些问题，当处于第一种 `for` 循环时，顺序执行也就意味着同步操作，请求一个一个被处理，当请求过多时也就意味着可能造成页面的卡顿。当处于第二种循环时，是并发请求但也意味着请求的顺序无法保证

> 在并发请求时，由于数据加载较快，第二种循环时按照顺序显示，实际情况则不一定

# 使用 `.map` 并发多个请求

`.map` 返回一个执行过后的新数组，并且数组函数的执行是并发的,执行效果如下

![map操作]({{site.baseurl}}/assets/img/15119385009852.jpg)

使用 map 操作简化并发对顺序无要求时的情况

```js
 function getinfoByUrl(urlArray) {
        urlArray.map(function(url) { getText(url).then(addText); });
    };
```


## Promise 中 `.all` 的使用

Promise.all() 方法是 Promise 对象上的静态方法，该方法的作用是将多个 Promise 对象实例包装，生成并返回一个新的 Promise 实例。

`.all` 通过一个 Promise 数据，并发进行多个 promise 请求，然后返回与原始 Promise 顺序相同的数值数组

Promise.all 等待所有 Promise 实例都变为 resolve（或某个 Promise 出现失败）时结束。当请求中有一个 promise 失败，则这个 `.all` 的请求就是失败的，无论其他的 promise 是否成功

`.all` 方法返回和原始 Promise 顺序相同的数值数组，并且是并发请求。这也在保证顺序的同时并发请求

```html

    // 首先 map 方法提供了同是进行并发请求的数组
    // all 方法保证了数组中的方法位置的固定，并等待所有的Promise 执行完成
    function getinfoByUrl(urlArray) {
        return Promise.all(urlArray.map(getText));
    };

    getUrlData(milliseconds)
        .then(getinfoByUrl)
        .then(function(infoArray) {
            // 然后将数据同步的加入到页面中
            console.log(infoArray);
            infoArray.forEach(function(info) {
                console.log(info);
                addText(info);
            });

        })
        .catch(function(error) {
            console.log("出现异常");
        });
    </script>
</body>

</html>
```


