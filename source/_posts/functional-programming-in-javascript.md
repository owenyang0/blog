title: "Functional Programming in JavaScript"
date: 2015-04-12 00:09:44
tags: [FP, JavaScript, Functional Programming, Ramda]
---

## Agenda

- What is Functional Programming?
- How Functional Programming Differs From Other Paradigms
- Functional Programming in Javascript
- Advantages and Disadvantages of Functional Style
- Interaction of Functional and Object-Oriented Styles

----

## What is Functional Programming?

- **Restricted sense,** FP means programming without mutable variables, assignments, loops and other imperative control structures
- **Wider sense,** FP means focusing on the functions
- **Particularly,** functions can be values that are produced, consumed, and composed.

----

## Functional Programming Languages

Functions in a FP languages are first-class citizens. This means:

- they can be defined anywhere, including inside other function
- like any other values, they can be passed as parameters to functions and returned as results
- as for other values, there exist a set of operators to compose functions

----

## Some languages

**Restricted sense:**

- Pure Lisp, XSLT, XPath, XQuery, FP

**Wider sense:**

- JavaScript
- Lisp, Scheme, Racket, Clojure
- SML, F#
- Haskell (full language)
- Scala

----

## OOP vs FP

OO focuses on the differences in the data, while FP concentrates on consistent data structures.

----

### Object-Oriented

- Data and the operations upon it are tightly coupled
- Objects hide their implementation of operations from other objects via their interfaces
- The central model for abstraction is the data itself
- The central activity is composing new objects and extending existing objects by adding new methods to them

----

### Functional

- Data is only loosely coupled to functions
- Functions hide their implementation, and the language’s abstractions speak to functions and the way they are combined or expressed
- The central model for abstraction is the function, not the data structure.
The central activity is writing new functions

----

## Declarative vs Imperative

One main distinguishing characteristics of functional programming languages is that they describe what they want done, and not how to do it. OO, inside its methods, still uses mostly imperative techniques.

----

### Imperative programming is about

- modifying mutable variables
- using assignment
- and control structures such as if-then-else, loops, break, continue, return.

----

The most common informal way to understand imperative programs is as instruction sequences for a Von Neumann computer

Processor <---- bus ----> Memory

----

### Declarative programming is about

- concentrate on functions
- avoid mutations (mutation can bestroy useful laws)
- have powerful ways to abstract and compose functions

----

### Imperative style
```
var sumOfSquares = function(list) {
    var result = 0;
    for (var i = 0; i < list.length; i++) {
        result += square(list[i]);
    }
    return result;
};

console.log(sumOfSquares([2, 3, 5]));
```

### Functional style
```javascript
var sumOfSquares = pipe(map(square), reduce(add, 0));

console.log(sumOfSquares([2, 3, 5]));
```

----

## Functional Features in JavaScript
### Easily available in Javascript

- First-class functions
- Lambdas/Anonymous Functions with closures
- Compact, even terse, functions

### Possible to accomplish in JavaScript

- Mostly stateless processing
- Side-effect-free functions calls

----

### Not available in JavaScript

- Performant recursion through tail call optimization
- Pattern matching (Haskell, Erlang)
- Lazy Evaluation (Miranda, Haskell)
- Homoiconicity (mostly LISP-like languages?)

----

### Shared examples

This calculates the odds of choosing the correct `n` numbers out of the `p` possibilities.

###  iterative version
```javascript
function odds(n, p) {
    var acc = 1;
    for(var i = 0; i < n; i++) {
        acc *= (n - i) / (p - i);
    }
    return acc;
}

console.log(odds(3, 10)); //=> (3/10) * (2/9) * (1/8) => (1/120) => 0.008333...
```

----

### Recursive version

```javascript
// Recursive version

function odds(n, p) {
    return (n == 0) ? 1 : (n / p) * odds(n - 1, p - 1);
}

console.log(odds(3, 10)); //=> (3/10) * (2/9) * (1/8) => (1/120) => 0.008333...
```

- recursive functions are often much more elegant than their iterative cousins.
- Unfortunately, they often don't perform as well. All the overhead of creating stack contexts for function calls tends to add up. But certain kinds of recursive calls can be easily optimized.

----

### tail recursive
```
var odds = (function(){
    var odds1 = function(n, p, acc) {
        return (n == 0) ? acc : odds1(n - 1, p - 1, (n / p) * acc);
    }

    return function(n, p) {
        return odds1(n, p, 1)
    }
})();
```


Note that the recursive call in **odds1** is the last statement in its branch of the function. If this is true for all recursive calls, then the function is **tail-recursive**, and the compiler can replace the entire set of nested calls with simple **JUMP** operations.

----

## Functional Programming in Javascript

With first-class function, closures, and anonymous functions, Javascript allows us to do a great deal of functional programming, even if we don't have things like pattern matching and homoiconicity.

There are some tools built in to modern Javascript environments, and it's straightforward to roll your own.

- Ramda
- Underscore, Lo-Dash

----

## Using Functional Techniques in JavaScript

Example will be a Task List application, fetching something like the following data from the server:

```javascript
var data = {
    result: "SUCCESS",
    interfaceVersion: "1.0.3",
    requested: "10/17/2013 15:31:20".
    lastUpdated: "10/16/2013 10:52:39",
    tasks: [
        {id: 104, complete: false,            priority: "high",
                  dueDate: "11/29/2013",      member: "Scott",
                  title: "Do something",      created: "9/22/2013"},
        {id: 105, complete: false,            priority: "medium",
                  dueDate: "11/22/2013",      member: "Lena",
                  title: "Do something else", created: "9/22/2013"},
        {id: 107, complete: true,             priority: "high",
                  dueDate: "11/22/2013",      member: "Mike",
                  title: "Fix the foo",       created: "9/22/2013"},
        {id: 108, complete: false,            priority: "low",
                  dueDate: "11/15/2013",      member: "Punam",
                  title: "Adjust the bar",    created: "9/25/2013"},
        {id: 110, complete: false,            priority: "medium",
                  dueDate: "11/15/2013",      member: "Scott",
                  title: "Rename everything", created: "10/2/2013"},
        {id: 112, complete: true,             priority: "high",
                  dueDate: "11/27/2013",      member: "Lena",
                  title: "Alter all quuxes",  created: "10/5/2013"}
        // , ...
    ]
};
```

----

## Goal
```
tasks: [
        {id: 104, complete: false,            priority: "high",
                  dueDate: "11/29/2013",      member: "Scott",
                  title: "Do something",      created: "9/22/2013"},
        {id: 105, complete: false,            priority: "medium",
                  dueDate: "11/22/2013",      member: "Lena",
                  title: "Do something else", created: "9/22/2013"},
    // , ...
]
```


The goal will be a function that accepts a `member` parameter, then fetches the data from the server (or from some application cache), chooses the tasks for that member that are not complete, returns their ids, priorities, titles, and dues dates, sorted by due date.

- asynchronous, we'll hook everything together with promises.
- ignore all error-checking

----

## Imperative Approach

```javascript
var getIncompleteTaskSummariesForMember_imperative = function(memberName) {
    return fetchData()
        .then(function(data) {
            return data.tasks;
        })
        .then(function(tasks) {
            var results = [];
            for (var i = 0, len = tasks.length; i < len; i++) {
                if (tasks[i].member == memberName) {
                    results.push(tasks[i]);
                }
            }
            return results;
        })
        .then(function(tasks) {
            var results = [];
            for (var i = 0, len = tasks.length; i < len; i++) {
                if (!tasks[i].complete) {
                    results.push(tasks[i]);
                }
            }
            return results;
        })
        .then(function(tasks) {
            var results = [], task;
            for (var i = 0, len = tasks.length; i < len; i++) {
                task = tasks[i];
                results.push({
                    id: task.id,
                    dueDate: task.dueDate,
                    title: task.title,
                    priority: task.priority
                })
            }
            return results;
        })
        .then(function(tasks) {
            tasks.sort(function(first, second) {
                return first.dueDate - second.dueDate;
            });
            return tasks;
        });
};

```

----

## Object-Oriented Approach
```javascript
// main method
var getIncompleteTaskSummariesForMember_objectOriented = function(memberName) {
   return fetchData()
       .then(function(data) {
           var taskList = new TaskList(data.tasks);
           taskList.chooseByMember(memberName);
           taskList.chooseByCompletion(false);
           var newTaskList = taskList.getSummaries();
           newTaskList.sort(new TaskListSorter("dueDate"));
           return newTaskList.tasks;
       });
};
```

----

```
var TaskList = (function() {
    var TaskList = function(/*Task[]*/ tasks) {
        this.tasks = tasks;
    };
    TaskList.prototype.chooseByMember = function(memberName) {
        var results = [];
        for (var i = 0, len = this.tasks.length; i < len; i++) {
            if (this.tasks[i].member === memberName) {
                results.push(this.tasks[i]);
            }
        }
        this.tasks = results;
    };
    TaskList.prototype.chooseByCompletion = function(completion) {
        var results = [];
        for (var i = 0, len = this.tasks.length; i < len; i++) {
            if (this.tasks[i].complete == completion) {
                results.push(this.tasks[i]);
            }
        }
        this.tasks = results;
    };
    TaskList.prototype.getSummaries = function() {
            var results = [], task;
            for (var i = 0, len = this.tasks.length; i < len; i++) {
                task = this.tasks[i];
                results.push({
                    id: task.id,
                    dueDate: task.dueDate,
                    title: task.title,
                    priority: task.priority
                })
            }
            return new TaskList(results);
        };

        TaskList.prototype.sort = function(/*TaskListSorter*/ sorter) {
            this.tasks.sort(sorter.getSortFunction());
        };

        return TaskList;
    }());
```

----

```
    var TaskListSorter = (function()  {
        var TaskListSorter = function(propName) {
            this.propName = propName;
        };
        TaskListSorter.prototype.getSortFunction = function() {
            var propName = this.propName;
            return function(first, second) {
                return first[propName] < second[propName] ? -1 :
                       first[propName] > second[propName] ? +1 : 0;
            }
        };

        return TaskListSorter;
    }());
```

The contents of the functions are much the same; it's the way they are organized that varies.

----

## Converting to Functional Code

The process for the remainder of this talk will be to convert this code into concise, readable, functional code, one block at a time, explaining some of the basic building blocks of functional programming as we go. First up is this little function:
```javascript
.then(function(data) {
    return data.tasks;
})
```

Functional version
```javascript
.then(prop('tasks'))
```

So the obvious question, then, is, what is the **prop** function?

----

## The prop function
```javascript
module.exports = _curry2(function prop(p, obj) { return obj[p]; });
```

Our **then** call needs a function, so **curry** must be doing something interesting with this function, which should return an object propery, to instead returning a new function. So we need to take a detour to discuss **curry** a bit.

----

## Currying Functions

Currying is the process of converting functions that take multiple arguments into ones that, when supplied fewer arguments, return new functions that accept the remaining ones.

```javascript
var addFourNumbers = function(a, b, c, d) {
  return a + b + c + d;
};

var curriedAddFourNumbers = R.curry(addFourNumbers);
var f = curriedAddFourNumbers(1, 2);
var g = f(3);
g(4); //=> 10
```

----

## Back to get

Now that we understand curry, we can see that a manually curried version of this function might look like this:
```javascript
var prop = function(prop) {
    return function(obj) {
        return obj[prop];
    };
};
```

And that means that our new **get('tasks')** is equivalent to
```javascript
function(obj) {
    return obj['tasks'];
}
```

----

## Filtering

```javascript
.then(function(tasks) {
    var results = [];
    for (var i = 0, len = tasks.length; i < len; i++) {
        if (tasks[i].member == memberName) {
            results.push(tasks[i]);
        }
    }
    return results;
})

```

----

Functional version
```javascript
.then(filter(function(task) {
    return task.member == memberName;
}))

```

But we've done something more important too: We've moved the focus from iteration and updating the state of a local collection to the real point of this block: choosing the tasks with the proper member property.

One of the most important features of functional programming is that it makes it easy to shift focus in this manner.

----

## Rejecting elements

The next block is similar, except that instead of using filter, we will use reject, which behaves exactly the same except that it chooses those members of the list that don't match the predicate. We replace this code:
```javascript
.then(function(tasks) {
    var results = [];
    for (var i = 0, len = tasks.length; i < len; i++) {
        if (!tasks[i].complete) {
            results.push(tasks[i]);
        }
    }
    return results;
})

```
with this:

```javascript
.then(reject(function(task) {
    return task.complete === true;
)))

```

----

## Refactoring

A reasonable question would be why with didn't do this instead, which would work equally well:

```
.then(filter(function(task) {
    return task.complete !== true;
))

```

The reason is that the similarity between these two blocks will offer us a chance to refactor our code into something still more descriptive:

```
function(task) {
    return task.member == memberName;
}

```

```
function(task) {
    return task.complete === true;
)

```
Both of these functions accept an object and return a boolean that describes whether a particular property of the object has a given value. Perhaps a good name for a function that generates such functions would be **propEq**.

----

## Implementing propEq

Simplest Approach
```javascript
var propEq = function(prop, val) {
    return function(obj) {
        return obj[prop] === val;
    };
}

```
This works, and we could leave it there, but we're going to take another detour into a popular style of functional programming known as **points-free** coding.

----

## Points-free definitions

With the functions **add** (which adds two numbers) and **reduce** (which runs the supplied function against an accumulator and each element of the list, feeding the result of each call into the next one and returning the final result), we can easily define a **sum** function like this:

```javascript
var sum = function(list) {
    return reduce(add, 0, list);
};

```

Because of the automatic currying, though, the following is entirely equivalent:

```javascript
var sum = reduce(add, 0);
```

This is the **points-free** style, defining functions without ever making direct reference to their arguments.

----

**Wikipedia**
>
Tacit [point-free] programming is a programming paradigm in which a function definition does not include information regarding its arguments, using combinators and function composition (but not ?-abstraction) instead of variables.

There are plenty of reasons to like it, but the most important one might just be the simplicity. There is a great deal to be said for elegant, readable code.

----

## A points-free version of propEq

Can we redefine the following in a points-free style?

```javascript
var propEq = function(prop, val) {
    return function(obj) {
        return obj[prop] === val;
    };
}

```

Here's a version that is closer to points-free, removing the direct reference to obj:
```javascript
var propEq = function(prop, val) {
    return compose(eq(val), get(prop));
}

```
Huh? What? **compose**? **eq**?

**eq** is easy: like all good functions of multiple parameters, it's curried, and it simply reports whether its two arguments are equal. So **eq(val)** is a function which reports whether its parameter has the same value as does **val**. But now we need to discuss **compose**.

----

## Functional composition

In mathematics **f ∘ g** (pronounced "f composed with g") is the function that given **x**, returns **f(g(x))**.

So if we follow the mathematical model **compose(add1, square)(x)** should equal **add1(square(x))**.


Simplest Implementation

```javascript
var compose = function(f, g) {
    return function(x) {
        return f(g(x));
    };
};

```

Note that **Ramda** also defines **pipe**, which does much the same thing, but runs the functions in the opposite order. So **pipe(add1, square)(x)** equals **square(add1(x))**. Both styles have their uses.

----

## Back to propEq

So now this definition makes sense:

```javascript
var propEq = function(prop, val) {
    return pipe(get(prop), eq(val));
}
```

Note the switch from **compose** to **pipe**.

This gives us a further way to clean it up, and make it entirely **points-free**, using a useful feature of Ramda we haven't seen implemented in other libraries, which we call (for now) **callWith**. Used like **callWith(fn, transformer1, ... transformerN)**, this returns a function which accepts N parameters, feeds them to the respective transformers, and then calls **fn** using the results of all these. This gives us the final version of propEq:

```javascript
var propEq = callWith(pipe, get, eq);
```

----

## Definition of propEq
```
module.exports = _curry3(function propEq(name, val, obj) {
  return _eq(obj[name], val);
});
```

----

## Using our new function

Still, all these explanations aside, it was only a minute or two of work to do this refactoring and arrive at a fairly simple version of **propEq**. We would plug it back in like this:

```javascript
var propEq = callWith(pipe, get, eq);

// ...

.then(filter(propEq('member', memberName)))
.then(reject(propEq('complete', true)))

```

----

## New objects from old

The next block we wanted to update looked like this:

```javascript
.then(function(tasks) {
    var results = [], task;
    for (var i = 0, len = tasks.length; i < len; i++) {
        task = tasks[i];
        results.push({
            id: task.id,
            dueDate: task.dueDate,
            title: task.title,
            priority: task.priority
        })
    }
    return results;
})

```
Functional version:

```javascript
.then(map(pick(['id', 'dueDate', 'title', 'priority'])))
```

You're a smart bunch, right? I probably don't even have to explain what pick does, right?

----

## The **map** function

One of the most fundamental functions used in FP is **map**, which is used to convert one list into a related one by running the same function against each member.

```javascript
var fiveSquares = map(square, [1, 2, 3, 4, 5]); // => [1, 4, 9, 16, 25];

var shouts = map(toUpperCase, ["oy", "vey"]); //=> ["OY", "VEY"]

```

There isn't much more to say about **map**, but it's important to point out that this function and **reduce**, which we mentioned briefly earlier, are among the most important functional programming tools around.

----

## Partial clones of our objects

We used map like this:

```javascript
.then(map(pick(['id', 'dueDate', 'title', 'priority'])))
```

The magic of currying is again at work here:

- **pick** accepts a list of properties and an object and return a partial clone, copying those properties from the original. Since we just pass in the properties, this curried function returns a new function that accepts an object and returns that partial clone.
- **map** accepts a function and a list an applies the function to the list. But because it's curried, and because we supply only the function generated by the curried pick, this one also returns a new function which will accept a list and create these partial clones of each element in the list.
- This function is passed to **then**, which will simply pass along its parameter (whenever that becomes ready) to our function and "return" the result of running our function against it. (We simply know because of the way prior calls have been built that this will be a list of tasks.)

----

## A final task of some sort

The last segment we wanted to convert looked like this:

```
.then(function(tasks) {
    tasks.sort(function(first, second) {
        return first.dueDate - second.dueDate;
    });
    return tasks;
});

```
Sorts the list according to a key generated by the supplied function.

```javascript
var sortByFirstItem = R.sortBy(prop(0));
var pairs = [[-1, 1], [-2, 2], [-3, 3]];
sortByFirstItem(pairs); //=> [[-3, 3], [-2, 2], [-1, 1]]
```

----

## Again with the curry

Again, we take advantage of the fact that our important functions are curried, and use **prop('dueDate')**. This creates a function that, fed one of our task objects, returns its due date. We can feed this into **sortBy** to get the following:

```
.then(sortBy(prop('dueDate')))
```

----


While this is not a lot less code than the original:

```javascript
.then(function(tasks) {
    tasks.sort(function(first, second) {
        return first.dueDate - second.dueDate;
    });
    return tasks;
});
```

it clearly is a savings. However, much more importantly, the code is all clearly aimed at our problem.

----

## Recap – Imperative

```javascript
var getIncompleteTaskSummariesForMember_imperative = function(memberName) {
    return fetchData()
        .then(function(data) {
            return data.tasks;
        })
        .then(function(tasks) {
            var results = [];
            for (var i = 0, len = tasks.length; i < len; i++) {
                if (tasks[i].member == memberName) {
                    results.push(tasks[i]);
                }
            }
            return results;
        })
        .then(function(tasks) {
            var results = [];
            for (var i = 0, len = tasks.length; i < len; i++) {
                if (!tasks[i].complete) {
                    results.push(tasks[i]);
                }
            }
            return results;
        })
        .then(function(tasks) {
            var results = [], task;
            for (var i = 0, len = tasks.length; i < len; i++) {
                task = tasks[i];
                results.push({
                    id: task.id,
                    dueDate: task.dueDate,
                    title: task.title,
                    priority: task.priority
                })
            }
            return results;
        })
        .then(function(tasks) {
            tasks.sort(function(first, second) {
                return first.dueDate - second.dueDate;
            });
            return tasks;
        });
      }
```

----

## Recap – Functional

```javascript
var getIncompleteTaskSummariesForMemberFunctional = function(memberName) {
    return fetchData()
        .then(get('tasks'))
        .then(filter(propEq('member', memberName)))
        .then(reject(propEq('complete', true)))
        .then(map(pick(['id', 'dueDate', 'title', 'priority'])))
        .then(sortBy(get('dueDate')));
};

```

----

## Most Important Functions

We've seen what are probably the most important functions in functional programming:

- map
- reduce
- filter
- compose
- curry

There's one that to some might be conspicuous by it's absence:

- forEach
Maybe it is not actually appropriate to functional programming, as its main purpose is to help you achieve side-effects. But many libraries do include one.

----

### What We Gain from FP Style
- Elegance and simplicity
- Easier decomposition of problems
- Code more closely tied to the problem domain

**And through these, we can also achieve**

- Straightforward unit testing
- Easier debugging
- Simple concurrency
