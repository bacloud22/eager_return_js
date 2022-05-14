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
<br>
<hr>
<br>

### Proposale

In the context described above, I would like to see a new kind of `return` defined and added to JavaScript.  

I think defining a `return` that goes through the stack to the top most calling code is unfeasible and/or too confusing. We should think of an agreement between the caller and the callee function so that the developer could decide clearly declare the particular situation described earlier between a caller and a callee function.

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

*(I can't think of now of a structure to defind `like`)*
But I can think of something similar to:

`like obj: [obj.key > 40]` for instance.





