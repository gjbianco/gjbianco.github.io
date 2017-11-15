---
title: 'JavaScript: A Repair Guide'
date: 2016-11-24 13:51:25
tags:
---
(Originally posted on the Red Hat Developers Blog: http://developers.redhat.com/blog/2016/04/27/javascript-a-repair-guide/)

## Introduction

You're a web developer. It's likely that you have written some of that nasty front-end JavaScript (JS) stuff. You probably wish your code would just work how you expect and you wonder why buttons get moved across the page, or disappear, when all you were trying to do is validate a form. Part of that is probably your Cascading Style Sheets (CSS); however, it's just as likely that…

## You're writing bad JavaScript

This is the part where you tell me all about how JavaScript is a horrible language, all about how it does "this and that" wrong, and that I'm some sort of hipster wannabe developer for using it, much less liking it.

I could respond in kind about how, yes, there are some bad parts to JavaScript, or that there are certain parts that are really neat and worthwhile (there are!) – not to mention, you can write poor code in any language – but we'll save that for another day. For right now, you have a script that is breaking your page, and, like it or not, you are stuck using JavaScript. You might as well learn how to fix some of those bad habits that are costing you so much time and headache.

Without further ado, let's get into…

## Scopes

JavaScript (JS) uses C-style blocks for its syntax. This trips up a lot of new JS developers as they assume those blocks mean scoping works the same way as in similarly-styled languages (and they would be wrong).

An iterating variable in your for-loop is available to everything inside your function! What's worse is that you probably have it right inside of a script by itself, and that you probably haven't defined a function at all. This means that your iterating variable is essentially global to your page.

One common error is forgetting to add the var keyword in front of your variable declarations. In this case, the variable goes on the global scope! Basically, for similar reasons as above (scope pollution), you don't want to do this. Be sure to use var to declare all of your variables!

For example, let's say I have a script I'm including on a page that simply has the following:

```javascript
// position A
for(var count = 0; count < 10; count++) {
  // position B
  alert('counter is at ' + count);
}

function foo() {
  var hidden = 'bar';
  // position C
}
// position D
```

In which positions is count found on my scope? Surprise, all of them! In JS, code blocks do not create a new scope, but functions do. So, the code block for my for-loop is not creating a new scope. As written above, I could reference count anywhere in the entire script.

But what about the variable named hidden? Where is that available? Since it is declared only in the foo function, it is only available at position C.
Now, even if you understand why count is found at B and C, you might still be confused as to why it's available at A.

That brings us to the topic of…

## Hoisting

This is where we really venture out of the realm of most C-style languages. The JavaScript interpreter does something pretty counter-intuitive when it hits a function containing the varkeyword. For every variable declaration, the interpreter "hoists" that declaration to the beginning of the scope (the function) and initializes it as undefined. They are hoisted in the same order as they appear in the scope. Let's look at that same snippet from the first example, but this time we'll display it as the interpreter sees it.

```javascript
var count = undefined;
// position A
for(count = 0; count < 10; count++) {
  // position B
  alert('counter is at ' + count);
}

function foo() {
  var hidden = undefined;
  hidden = 'bar';
  // position C
}
// position D
```

Reordering the statements makes it a lot easier to see how count is available at all 4 positions. This is also the reason why it is common for people to declare all of their variables at the top of a function (leaving off the " = undefined" initialization, since it's redundant).

## Function Declarations VS Function Expressions

In the previous examples, we defined a function called foo. This is a pretty common syntax for writing named functions. However, one of the more powerful features of JS is treating functions like first-class citizens. Specifically, JS lets us define a function pretty much anywhere. These are called Lambda functions, which is a fancy term for anonymous functions (you know, 'cause they don't have names).

Let's take a look at some code that you may be familiar with. Specifically, this snippet runs our function every 5 seconds.

```javascript
setInterval(function() {
  alert('hello world');
}, 5000);
```

This is a very basic example of an anonymous function in JavaScript. The arguments to setInterval are a new function (a "callback function") that we have defined in-line and the number of milliseconds between each call to that function.

Our function doesn't have a name, but we can optionally give it one for debugging purposes by specifying a name directly after the function keyword. This function, when run, simply alerts the user with our message. Keep in mind that the function doesn't actually run at this point — it has only been defined; the user won't see our alert until 5 seconds have gone by (the first timer interval), and then continually every 5 seconds.

Passing functions around in this manner is very common, as a lot of JavaScript APIs expect you to pass functions as callbacks; this is a strategy commonly used to perform asynchronous work.

It's not obvious, but there are some important differences between our foo function and our most recent anonymous function. The biggest difference is that foo is an example of a function declaration and our anonymous function is an example of a function expression.

Let's look at a more direct comparison:

```javascript
var foo = function() {
  alert('foo ran');
};

function bar() {
  alert('bar ran');
}
```

Both of these definitions assign a function to a variable, which can be called just like any other function (i.e. `foo()` ). Again, the first is a function expression and the second is a function declaration.

Even more important is the concept of how hoisting applies to each of these function definition techniques:

 * For a function declaration – `function bar() { ... }`: The entire function (including the function body) gets hoisted to the top.
 * For a function expression – `var foo = function() { ... }`: This is really just variable assignment in disguise; only the variable declaration (`var foo`) gets hoisted (no function body).

Again, let's look at how the interpreter sees this:

```javascript
function bar() {
  alert('bar ran');
}
var foo = undefined;

foo = function() {
  alert('foo ran');
}
```

Okay, time to finally circle back to see how all of this affects…

## Isolating Your Scripts

Now that we understand some of the "less intuitive" parts of scopes in JS, we can begin to clean up our scripts. Chances are that you've run into at least one issue with your scripts that was caused by improper scoping.

Let's say we have a basic HTML page with several scripts and that those scripts get loaded and run in the same order that they are declared; however, even though they are declared in different files, they all actually run within the same scope! What that means is that if one script assigns the value `foo` to a variable called a, then another script assigns the value `bar` to a variable called a, any future references to a within the first script will resolve to the value `bar`. Both of the a's are really the same variable, even though they are declared separately in two scripts! Let's look at an example.

```javascript
// script 1
var a = "foo";
setInterval(function() {
  alert(a);
}, 5000);


// script 2
var a = "bar";
```

In this case, we have both scripts included in our HTML page and in the above order. What will our alert tell us? Since the second script runs before the function actually gets called, it has a chance to change the variable. Our alert actually tells us `bar`, which is probably not what we intended.

How do we fix this?

The easiest way to fix this is to move our variable declaration in script 1. This moves the a for our alert out of the global scope.

Our script 1 now looks like the following:

```javascript
// script 1
setInterval(function() {
  var a = "foo";
  alert(a);
}, 5000);
```

This ensures that a resolves to `foo` every time our function runs. Beyond that, it does not change the value of a in script 2 since we are declaring a new a within our new scope (the anonymous function). This is the best way to keep the scopes of our scripts isolated – using functions as scoping barriers.
The cool thing is that you can declare and run an anonymous function pretty easily. All it requires is some parentheses:

```javascript
(function() {
  var hidden = "i'm completely unavailable outside of this script/function!";
  alert(hidden);
})();
```

In our above example, hidden is, in fact, only available within the confines of that anonymous function; an anonymous function that we call immediately after defining it is referred to as an Immediately-Invoked Function Expression (IIFE), and that is what we are doing in this example.

Using an IIFE, we can (and should!) isolate all of our scripts' scopes from one another.

## Miscellaneous Tips

In order to keep up with your var usage (and other gotchas), I would recommend using some form of "linter" on your code (e.g. jshint). A "linter", simply put, analyzes your code for bad things and warns you about them; there are lint plugins available for most text editors and IDEs.

It is also a good idea to lint your scripts as part of your build, but that is outside the scope of this article.

You may have seen scripts that contain "use strict"; at the top; this puts the interpreter into strict mode (if supported). Strict mode is not supported in all browsers, but it can be helpful for preventing certain errors, so you probably want to use it. If you use it in conjunction with the immediately-called anonymous function above, you should include the "use strict" line within that function; otherwise, you set all scripts after that point to run in strict mode, not just your own script (or even ones that you wrote!). This could cause unexpected behavior depending on how those scripts were written.

For information on what changes strict mode introduces, refer to the W3 schools article on it.

You are now ready to go and face your scripts with a new understanding of JavaScript scopes, hoisting, and should have a few tips to keep your global scope clean. Beyond these techniques, just remember that JavaScript is its own programming language. As tempting as it might be, don't assume that it works like another language just because it looks like one. You'll be safer in the long run.
