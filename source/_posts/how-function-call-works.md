title: "function调用是如何发生的？"
date: 2017-10-18
categories:
- 内核
---

To call a function such as foo(6, x+1)...

1. Evaluate the actual parameter expressions, such as the x+1, in the caller's context.
2. Allocate memory for foo()'s locals by pushing a suitable "local block" of memory onto a runtime "call stack" dedicated to this purpose. For
parameters but not local variables, store the values from step (1) into the appropriate slot in foo()'s local block.
3. Store the caller's current address of execution (its "return address") and switch execution to foo().
4. foo() executes with its local block conveniently available at the end of the call stack.
5. When foo() is finished, it exits by popping its locals off the stack and "returns" to the caller using the previously stored return address. Now the
caller's locals are on the end of the stack and it can resume executing.

Whys:

- This is why local variables have random initial values — step (2) just pushes the whole local block in one operation. Each local gets its own area
of memory, but the memory will contain whatever the most recent tenant left there. To clear all of the local block for each function call would be
too time expensive.
- The "local block" is also known as the function's "activation record" or "stack frame". The entire block can be pushed onto the stack (step 2), in a
single CPU operation — it is a very fast operation.
