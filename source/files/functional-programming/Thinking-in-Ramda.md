# Think in Ramda 系列摘要
# Part 1
> By "first-class", I mean that functions can be used in the same way as other kinds of values. You can:
* refer to them from constants and variables
* pass them as parameters to other functions
* return them as results from other functions

> pure function

> Immutability

> When I’m working in an immutable fashion, once I initialize a value or an object I never change it again. That means no changing elements of an array or properties of an object.

> If I need to change something in an array or object, I instead return a new copy of it with the changed value. Later posts will talk about this in great detail.

# Part 2
> The new function takes the same number of arguments as the first function it is given. It then “pipes” those arguments through each function in the list. It applies the first function to the arguments, passes its result to the second function and so on. The result of the last function is the result of the pipe call.

> Note that all of the functions after the first must only take a single argument.

> By combining several functions in specific ways, we can start to write more powerful functions.

> pipeline

> The new function takes the same number of arguments as the first function it is given. It then “pipes” those arguments through each function in the list. It applies the first function to the arguments, passes its result to the second function and so on. The result of the last function is the result of the pipe call.

> Note that all of the functions after the first must only take a single argument.

> By combining several functions in specific ways, we can start to write more powerful functions.

Ramda API:
* `compose`, `pipe`
* `ifElse`

# Part 3
> Functions that take or return other functions are known as “higher-order functions”.

Ramda API:
* `partial`, `partialRight`
* `curry`
* `tap`

> In Ramda, a curried function can be called with only a subset of its arguments, and it will return a new function that accepts the remaining arguments. If you call a curried function with all of its arguments, it will call just call the function.

> Notice that to make curry work for us, we had to reverse the argument order. This is extremely common with functional programming, so almost every Ramda function is written so that the data to be operated on comes last.
You can think of the earlier parameters as configuration for the operation. 

# Part 4
**`impreative programming` VS `Declarative programming`**

> imperative programming is a style of programming where the programmers tell the computer what to do by telling it how to do it.
Declarative programming is a style of programming where the programmers tell the computer what to do by telling it what they want. The computer then has to figure out how to produce the result.

> Functional programming is considered a subset of declarative programming. In a functional program.

# Part 5
> pointfree style

# Part 6
**Topic: How to manipulate object in a immutable way by using Ramda?**

> Ramda won't change object directly. Instead, it provides some useful APIs to help us change object in a immutable way. If we change object by using these APIs, we can make sure that we always get a new object.

Ramda API:
* read property: `prop`, `pick`, `has`, `hasIn`, `path`, `propOr`, `pathOr`.
* add & update property: `assoc`, `assocPath`.
* delete property: `dissoc`, `dissocPath`, `omit`.
* transform property: `evolve`.
* merge object: `merge`.

# Part 7
**Topic: How to manipulate array in a immutable way by using Ramda?**

Ramda API:
* reading array elements: `nth`, `slice`, `contains`, `head`, `tail`, `last`, `init`, `take`, `takeLast`.
* add elements: `insert`, `append`, `prepend`.
* update elements: `update`, `adjust`.
* merge array: `concat`.
* remove elements: `remove`, `without`, `drop`, `dropLast`.

# Part 8
> lens is short for lenses.

> Lenses concludes by introducing the concept of a lens, a construct that allows us to focus on a small part of a larger data structure. Using the `view`, `set`, and `over` functions, we can read, update, and transform the focused value in the context of its larger data structure.

Ramda API:
* `lens`
* `view`, `set`, `over`

# Part 9
> If you’re interested in more advanced functional topics, here are some places you can go:

* Transducers: There’s [a good introductory article](http://simplectic.com/blog/2015/ramda-transducers-logs/) on parsing logs with transducers.

* Algebraic Data Types: If you’ve read much about functional programming, you’ll have heard about algebraic types and terms such as “Functor”, “Applicative”, and “Monad.” If you’re interested in exploring these ideas in the context of Ramda, check out the [ramda-fantasy](https://github.com/ramda/ramda-fantasy) project, which implements several data types that comply with the [Fantasy Land Specification](https://github.com/fantasyland/fantasy-land) (a.k.a. the Algebraic JavaScript Specification).