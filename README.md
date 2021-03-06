## Context

It is common to call a function to do a *task* and *return* results *as soon as possible*. Sometimes we call a function that calls other functions to look for that same results. In this particular situation, we need to return results to the top most code as soon as possible.

### Example

```js
function return42 () {
  return 42
}
```
and  
```js
// Calling function
function caller() {
  if (return42() === 42) return return42()
  else {
    // continue
  }
}
```

### Comments

By definition, every function would like to be deterministic and to have full control over its code and memory (of course we might need side effects through global variables or IO streams).  
This is to say it is uncommon (often uneeded) to let the callee function take control over its caller. I think it is unecessary and time wasting to find situations where we need that because of course we could find such situations but it is very uncommon in the mindset of computing algorithms.

Nevertheless, I think the example we raised earlier is very common, and well defined.

*(I think you already see where I'm heading to!)*  
<hr>

### Proposale

In the context described above, I would like to see a new kind of `return` defined and added to JavaScript.  

I think defining a `return` that goes through the stack to the top most calling code is unfeasible and/or too confusing. We should think of an agreement between the caller and the callee function so that the developer could decide clearly then declare the particular situation described earlier between a caller and a callee function.

*(We already had `async function_n` with `await function_n()` added to JavaScript without any problems but with new solutions !)*

My propositions is to add two keywords similar as `async` and `await` to describe this situation. Something like `give` and `found` (I'm not English native, maybe not the best words).

### Example

```js
function `give` (`like 42`) return42 () {
  `found` 42
}
```
and  
```js
// Calling function
function `give` (`like 42`) caller() {
  `found` return42()
  // otherwise just continue !! So no if-else block here ! 
  // continue
}
```

### Second example

![give-found](https://user-images.githubusercontent.com/10267332/168461783-ce9ee71a-2209-4a96-bf8d-8a69d28e519f.png)



*(I can't think now of a structure to define `like`)*
But I can think of something similar to:

`like obj: [obj.key > 40]` for instance.


### Edit 01:
- Visibility this is similar to try,catch/throw in regard of the flow of execution. 

### Edit 02: (use of try-catch, counter intuitive, as we are using throw error native flow of execution to return values. But it does the job)
@theScottyJam comment on this: 

I think you're right about this idea being fairly similar to try/catch/throw. In fact, I don't think it would be too much effort to implement something like this in userland.

Instead of this:

```js
function `give` caller(x, y) { ... }
```

write this:

```js
function caller(give, x, y) { ... }
```

And instead of this:

```js
`found` return42();
```

write this:

```js
give(return42());
```

Then use a utility receive() function to capture the eager-return value, and voila! With a small user-land library, you have this feature.

**Full example:**
```js
// The utility library

class LongReturn extends Error {
  constructor(value) {
    super('This got uncaught! That is not supposed to happen.');
    this.value = value;
  }
}

function give(value) {
  throw new LongReturn(value);
}

export function receive(callback) {
  try {
    return callback(give);
  } catch (err) {
    if (err instanceof LongReturn) return err.value;
    throw err;
  }
}

// ...elsewhere...

function fn1(give, randomBool) {
  return fn2(give, randomBool)
}

function fn2(give, randomBool) {
  if (randomBool) {
    return 'slow path';
  } else {
    give('long path');
  }
}

const randomBool = Math.random() > 0.5;
const result = receive(give => fn1(give, randomBool));

console.log(result);
```





