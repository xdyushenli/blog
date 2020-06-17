---
title: The Journey of React 15 Source Code
date: 2020-06-16 13:50:22
tags: [ react ]
---
We can write better code by reading good code.

# todo
There are more before...

# Children
onlyChildren

# ReactComponent.js
```js
ReactComponent.prototype.setState = function (partialState, callback) {
    this.updater.enqueueSetState(this, partialState);

    if (callback) {
        this.updater.enqueueCallback(this, callback, 'setState');
    }
};

ReactComponent.prototype.forceUpdate = function (callback) {
    this.updater.enqueueForceUpdate(this);

    if (callback) {
        this.updater.enqueueCallback(this, callback, 'forceUpdate');
    }
};
```
What can we learn about from code above?
Are there two kinds of queue in React? One is a state queue, the other is a callback queue.

# ReactPureComponent.js
## What ReactPureComponent Do?
> `React.PureComponent` is similar to `React.Component`. The difference between them is that `React.Component` doesn't implement `shouldComponentUpdate()`, but `React.PureComponent` implements it with a shallow prop and state comparison.

## Dive into ReactPureComponent
```js
function ComponentDummy() {}
ComponentDummy.prototype = ReactComponent.prototype;
ReactPureComponent.prototype = new ComponentDummy();
ReactPureComponent.prototype.constructor = ReactPureComponent;
// Avoid an extra prototype jump for these methods.
Object.assign(ReactPureComponent.prototype, ReactComponent.prototype);
ReactPureComponent.prototype.isPureReactComponent = true;
```
todo: this paragraphs definitely has some grammatical errors...
These codes could be a little diffcult to understand. This snippet has two effects:
* Make `ReactPureComponent` and `ReactComponent` has inheritance.
* Make `ReactPureComponent` instance inherits `React.prototype`'s properties.

The final point direction will be like this:

todo: Pretend to have a image here...

> If that does't make sense, take a look at the following snippet and try to understand what's going on here:
```js
function ReactComponent() {
  console.log('I am a ReactComponent!');
}

ReactComponent.prototype.ReactComponentMethod = function() {
  console.log('This is a method on the ReactComponent.prototype');
};

function ReactPureComponent() {
  console.log('I am a ReactPureComponent!');
}

function ComponentDummy() {}

ComponentDummy.prototype = ReactComponent.prototype;
ReactPureComponent.prototype = new ComponentDummy();
ReactPureComponent.prototype.constructor = ReactPureComponent;

ComponentDummy.prototype.constructor(); // I am a ReactComponent!
ReactComponent.prototype.constructor(); // I am a ReactComponent!
ReactPureComponent.prototype.constructor(); // I am a ReactPureComponent!

const newPureComp = new ReactPureComponent(); // I am a ReactPureComponent!
newPureComp.ReactComponentMethod(); // This is a method on the ReactComponent.prototype
```

## Let's Dive a little Deeper
Two Further Questions:
todo: WHY WHY WHY!!!
* Why not just uses these codes? Why `ComponentDummy`?
```js
ReactPureComponent.__proto__ = ReactComponent.prototype;
```

* Why we need to assign `ReactComponent.prototype` to `ReactPureComponent.prototype`?
```js
// Avoid an extra prototype jump for these methods.
Object.assign(ReactPureComponent.prototype, ReactComponent.prototype);
```

These two questions are actually one same questions, and the better description is: Why take a long way to make `ReactPureComponent.prototype.__proto__` points at `ReactComponent.prototype`? We will copy `ReactComponent.prototype` to `ReactPureComponent.prototype` eventually anyway.

todo: edit this paragraphs.
I don't know why they did this, but I **DO KNOW** what these code does't do.**It would't rewrites `ReactPureComponent.prototype.constructor` property.**

I think the answer may in the [test file](https://github.com/facebook/react/blob/v15.4.1/src/isomorphic/modern/class/__tests__/ReactPureComponent-test.js).
```js
it('extends React.Component', () => {
    var renders = 0;
    class Component extends React.PureComponent {
      render() {
        expect(this instanceof React.Component).toBe(true);
        expect(this instanceof React.PureComponent).toBe(true);
        renders++;
        return <div />;
      }
    }

    ReactDOM.render(<Component />, document.createElement('div'));
    expect(renders).toBe(1);
});
```
As we can see, the `ReactPureComponent` should be both an instance of `ReactPureComponent` and `ReactComponent`. It implies that `ReactComponent` is extended by `ReactPureComponent`.

# ReactElement.js
todo
* article link: https://medium.com/@ericchurchill/the-react-source-code-a-beginners-walkthrough-i-7240e86f3030
* unpkg link: https://unpkg.com/browse/react@15.4.1/lib/ReactElement.js
* react github link: https://github.com/facebook/react/blob/v15.4.1/src/isomorphic/modern/class/__tests__/ReactPureComponent-test.js

# References
* [The React Source Code: a Beginner’s Walkthrough I](https://medium.com/@ericchurchill/the-react-source-code-a-beginners-walkthrough-i-7240e86f3030)
* [React Source Code V15.4.1](https://github.com/facebook/react/tree/v15.4.1)
* [React Documentation](https://reactjs.org/docs/react-api.html)

# More References (not read yet)
* [Using a function in setState instead of an object](https://medium.com/@wisecobbler/using-a-function-in-setstate-instead-of-an-object-1f5cfd6e55d1)
* [JavaScript Pseudoclassical Subclasses](https://yctercero.github.io/javascript-subclasses/)