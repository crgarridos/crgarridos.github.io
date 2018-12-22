Kotlin introduced very delightful features in its standard library and a lot of util functions in order to improve our productivity in crafting new code. As more as we use these feature, we are sometimes tempted to use it more and more. Personally, I think in the case when I wanted to get rid of extra lines, extra variable declarations, extra branching conditions and so on.

In this article, I want to address especially the use of the apply/with/let/also utility functions. Here their signatures as a reminder:

TODO apply/with/let/also signatures.

In my daily codebase, I used a lot. But it is important to have in mind that is not mandatory, and should not be used systematically whenever we can.  
Here I expose a brief list of when not abuse on  
Readability  
Conciseness

```kotlin
with(doSomething(myVariable).getResult()){
    member = if(this > 0) createSomething(this) else doSomethingElse(this)
}
```

When I see the code above, I get confused by the fact that I instinctively expect the variable `member` to be an actual member of `doSomething(myVariable).getResult()`, but I get stopped by this last and I ask me: What is `doSomething(myVariable).getResult()`? We may put this in a variable in order to give it a name.

```kotlin
val myResult = doSomething(myVariable).getResult()
with(myResult){
    member = if(this &gt; 0) createSomething(this) else createSomethingElse(this)
}
```

Good! I have a `myResult` as `this` and now I know that I want a result to be a positive number to create an actual value. But sadly I continue seeing in my mind that still `member` being affected to `this` from the wrapping `with`. I understand the practicity of using with but I see the actually use in this case is just avoiding to name a variable.

Let change the code to:

```kotlin
val myResult = doSomething(myVariable).getResult()
member = if(myResult > 0) createSomething(myResult) else createSomethingElse(myResult)
```
