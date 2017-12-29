extends: default.liquid

title: "Modern JS concepts: Arrow functions, the new and improved function literals"
date: 28 Dec 2017 12:00:00 +0100
timestamp: 2017/12/29
thumbnail: /assets/img/post-thumbnails/modern-js-arrow-functions.png
caption: An object demonstrating a use case for arrow functions
---

In the past, we would see a lot of this:

```javascript
someFunction(param, function() {
  // do something
});
```
Now, this gets a little bit cumbersome after a while. Not only are function expressions long, they include a kitchen sink of features that you probably won't need in all cases, like the `this` or a `new` constructor.

Thankfully, in EMCAScript 6, we got this whole new and improved arrow function. Let's take a look.<span data-separator></span>

## Syntax

The declaration of an arrow function goes like this:

```
(param1, param2 ... paramN) => expression
```

Arrow functions may remind you of the lambda or anonymous function syntax used various other programming languages like Haskell:

```javascript
someFunction(param, () => {
  // do something
});
```

As you can see, they're defined with the parameters wrapped in brackets, followed by an `=>` arrow and an expression. If your arrow function doesn't need any parameters, you can just use two empty brackets as I did above. Here's another example:

```javascript
var array = [1, 2, 3, 4];
var increment = 2;

var incremented = array.map(item => item + increment); // [3, 4, 5, 6]
```

When writing arrow functions like this (without using `{}` curly braces), you don't even need to use the `return` keyword, similar to how you would return a value in a language like Rust. It's implied that, since there's only one expression that needs to be evaluated, it should be returned.

In that example, we didn't use brackets around the argument `param`. When you only have one parameter, brackets are optional.

## Advantages

One of the things that makes arrow functions so useful is that they don't have their own `this` object. Consider the following:

```javascript
var object = {
  values: [2, 4, 6, 8],
  multiplyBy: 7,
  multiply() { // ES6 also let's us write functions in objects like this
    return this.values.map(function (value) {
      return value * this.multiplyBy;
    })
  }
}
```

However, if we try to run this, we'll get an `Array [ NaN, NaN, NaN, NaN ]` value. Why, you ask? Try taking a closer look at that anonymous function.

As I explained in [my article on closures](https://steemit.com/programming/@njms/closures-in-programming-what-are-they-and-how-do-they-work), Every nested function takes on the *lexical scope* of the function it's nested in. In other words, all values made available in the outer function exist in the inner function. For this reason, we might expect that the `this` object should be available as well. However, the anonymous function you're working in already has it's own `this` which takes superiority.

Though we might have access to `multiplyBy` in the outer function, getting to it within the inner function is a bit more difficult. Before ES6, people often used this hack:

```javascript
var self = this;

return this.values.map(function (value) {
  return value * self.multiplyBy;
})
```

Sure, this works, but it's a bit of a compromise. Thankfully, arrow functions solve this issue beautifully.

```javascript
var object = {
  values: [2, 4, 6, 8],
  multiplyBy: 7,
  multiply() {
    return this.values.map(value => value * this.multiplyBy);
  }
}
```

Since arrow functions don't have `this`, no referencing is required to access the `multiplyBy` value.

Perfect.

## Another (practical) use case

Let's take a look at this function I wrote to simplify the process of querying an API with an AJAX call using jQuery:

```javascript
function queryAPI(query, datatype, callback) {
  return $.ajax({
    url: query,
    dataType: datatype,
    success: callback.success,
    error: callback.error
  });
}
```

The function takes three parameters: the complete URL, the data type and a success and error callback wrapped in an object. We can call this function like so:

```javascript
queryAPI("https://api.example.com/some/query", "json", {
  success: function(data) {
    element.innerHTML = data;
  },
  error: function(error) {
    element.innerHTML = error;
  }
})
```

Now that we know more about arrow functions, things would look a lot nicer written like this:

```javascript
queryAPI("https://api.example.com/some/query", "json", {
  success: data => element.innerHTML = data,
  error: error => element.innerHTML = error
})
```

EMCAScript 6 is full of great features. Sure, many of them might not be supported natively in all browsers yet, but that doesn't mean it's too early to start!

## Further reading
 * [Arrow functions - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
 * [ES6 In Depth: Arrow functions](https://hacks.mozilla.org/2015/06/es6-in-depth-arrow-functions/)
 * [Arrow function vs function declaration / expressions: Are they equivalent / exchangeable? - Stack Overflow](https://stackoverflow.com/questions/34361379/arrow-function-vs-function-declaration-expressions-are-they-equivalent-exch)

 *Agree or disagree? Think I missed or poorly explained something important? Questions, comments or concerns? Leave a comment down below!*
