---
title: JavaScript Simplicity
date: 2017-02-28 13:54:55
tags:
---
## Introduction

Lately, I keep reading articles about how the JavaScript ecosystem has too many libraries, which creates a steep learning curve. A lot seem to be purely dismissive without offering a real solution. Others are pushing projects to address this issue, but never explain how their solution is actually a *good* one. If anything, isn't it counterintuitive to use a tool to address the problem of too many tools?

In fact, that idea is what I want to talk about.

It is very easy to get caught up in the never-ending spiral of "this tool doesn't do X, so I will get tool Y to fix that". Having an ecosystem with all sorts of tools for almost any problem is a blessing and a curse. It's great to have access to so many good libraries, but a growing list of dependencies often is more work than necessary. Moderation is key.

At the core of this issue is the idea that you have to use the latest, greatest, and "sexiest" frameworks in every single project you do. This is a very naive approach to coding, in general.

There are plenty of adages and sayings against dependency creep (e.g. "less is more", "keep it simple, stupid"). Basically, only include dependencies that you know will have positive returns on your development time and effort. Don't just blindly use a library because it's new or popular.

However, maybe that means it is worth looking into. Just keep in mind that you'll need some time to learn the tool. You may even need to learn the tool just to determine if it helps you. If that's the case, make sure you aren't treating everything like a nail with your new hammer.

If you are completely new to the ecosystem, it can be hard to determine a simple set of dependencies that will have those high returns.

Below is a list (in order!) of basic tools you should start with. I will try to include the name of appropriate "workshoppers" from [NodeSchool](https://nodeschool.io/). A workshopper is a collection of lessons that require you to do a small lab after each lesson. This practice is crucial for actually learning material.

## JavaScript

It may seem silly for me to include this here. If you take away nothing else from this post, take this: learn the fundamentals of JavaScript before trying to use it. I see so many people making the poor assumption that JavaScript *works* like other languages just because it *looks* like them. This is simply not true. A lot of people skip getting familiar with JavaScript and jump directly into their framework of choice. This accounts for most of the bad JavaScript code out there.

I highly recommend reading `JavaScript: The Good Parts` by Douglas Crockford. You don't have to understand every little function that gets listed, but you should be aware how things like scopes and hoisting work.

Applicable workshoppers: `javascripting`, `scope-chains-closures`, `js-best-practices`

## Node.js

Yes, this is different from learning JavaScript. Node is built on top of JS. That means there are plenty of functions, built in libraries, and techniques that are Node specific for you to learn. 

Applicable workshoppers: `learnyounode`, `how-to-npm`, `stream-adventure`

## Express

This is by far the most common Node module for setting up REST services. Express allows you to set up basic routes using a fantastic yet simple API. 

Applicable workshoppers: `expressworks`

## Mocha and Chai

Mocha is a test runner (so for actually setting up and running the tests). Chai is an assertion library, which means it is used to do the actual checks (e.g. to make sure a return value is a number greater than 0). Just by using these two, you have a very powerful unit testing framework.

If you need code coverage, you can set up Istanbul to run with mocha. You don't really have to learn much for Istanbul.

## Front end frameworks (Angular, React, etc.)

You should have a basic understanding of JavaScript and simple server side code before you start writing frontend code since most of the new ones will use AJAX to make calls to the backend. Personally, I prefer Angular 1.x, and am now learning Vue.js.