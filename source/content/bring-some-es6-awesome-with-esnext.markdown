---
title: Bring some ES6 Awesome with esnext
date: 2014-12-22 9:40 EST
image: http://cball.me.s3.amazonaws.com/es6-awesome.png
tags: ember, ember-cli, esnext, es6
---

---

ES6 is the next version of JavaScript. [esnext](https://github.com/esnext/esnext) is a library that allows you to ES6 functions today.

What does this mean if you’re an Ember developer? If you’re using ember-cli 0.1.3 or later, you can use it out of the box without any additional configuration thanks to [this Pull Request](https://github.com/stefanpenner/ember-cli/pull/2452).

Let’s look at three top features and some benefits provided by esnext that might eliminate the need to add CoffeeScript to your next project.

### Arrow Functions
If you’ve used CoffeeScript, arrow functions should be familiar. Typing the word function all of the time can get annoying. Using the fat arrow, you can use a much nicer syntax for writing functions.

~~~javascript
// The es6 way using esnext
{
  eat: (food) => {
    console.log('I am eating ' + food);
  }
}

// Compiled output
{
  eat: function(food) {
    console.log("I am eating " + food);
  }
}
~~~

When using functions that change the scope of this (such as `forEach`) you may have seen the following pattern:

~~~javascript
// this is common, but don’t do it.
var _this = this;
foods.forEach(function(food) {
  _this.eat(food);
}
~~~

Since `this` inside an arrow function is the same as this in surrounding code, we can make the above example better: 

~~~javascript
foods.forEach(food => {
  this.eat(food);
});
~~~

“I can already do that with CoffeeScript” you might say. True, but compare the code you end up with. Which do you think is easier to debug?

~~~javascript
// compiled JavaScript output with esnext
foods.forEach(function(food) {
  this.eat(food);
}.bind(this));

// compiled CoffeeScript output
foods.forEach((function(_this) {
  return function(food) {
    return _this.eat(food);
  };
})(this));
~~~

_One caveat to note_: because Ember extends the `function` prototype to provide the `property` and `observes` keywords, you can’t use the fat arrow with a `property` or `observes` declaration. For those, you’ll still need to use the `function` keyword.

For standard functions you’re good to go.

### Template Strings
One of the things that has always bothered me is the lack of string concatenation in JavaScript. ES6 Template Strings fix that:

~~~javascript
// standard JavaScript
Ember.Controller.extend({
  title: function(){
    var food = this.get('food');
    return "I am getting " + food + ".";
  }.property('food')
});

// the es6 way
Ember.Controller.extend({
  title: function() {
    var food = this.get('food');
    return `I am getting ${food}.`
  }.property('food')
});
~~~

Template strings can even span multiple lines.

~~~javascript
// invalid in standard JavaScript
"what kind
of " + nounFromUser + " do you
drive?"

// es6 template string
`what kind
of ${nounFromUser} do you
drive?`
~~~

They have some [other powerful features](http://mozintern.wordpress.com/2014/07/31/ecmascript-6-template-strings-2/) too.

### Destructuring
Destructuring is great when you need to set variables that have the same name as object properties. For example, say you need to configure options for a graph:

~~~javascript
// standard JavaScript
var graphWidth = config.graphWidth,
graphHeight = config.graphHeight,
marginTop = config.marginTop,
marginBottom = config.marginBottom

// the es6 way
var { graphWidth, graphHeight, marginTop, marginBottom } = config;
~~~

### CoffeeScript can already do this. Why use ES6/esnext?
1. The source code you write will look almost identical to the compiled output. Any differences are easy to follow. As a developer, this makes your life much easier when debugging an issue in your app!
1. Your file size will be smaller.
1. You won’t have to deal with unexpected implicit returns. For example, actions in Ember will bubble if you return a truthy value from them. Because CoffeeScript has an implicit return from the last line in a function, it is very easy to accidentally write code that will bubble up to parent controllers/routes.
1. If you extract a piece of your app to an addon, you will benefit from more community collaboration by using JavaScript.
1. ES6 module imports/exports are available without using backticks. 
1. Your code will be somewhat future proof. Since a core focus of esnext is api compatibility, when ES6 is available natively in all major browsers, the esnext library should be able to be removed and the code will continue to work. CoffeeScript has features that will keep it from fully adopting ES6, and it will likely require a rewrite.

If you’re converting from CoffeeScript, you’ll have to remember to use the word this, add commas in object definitions, parenthesis, and some semicolons. Outside of that, with the ES6 features provided by esnext, you probably won’t miss CoffeeScript at all.