As widely known, Kotlin introduced very delightful features and utils functions in its standard library in order to improve our productivity when crafting new code. As more as we use these features, we are sometimes tempted to use them more and more. Personally, I think in the in all the cases when I wanted to get rid of extra lines, extra variable declarations, extra branching conditions and so on. 

In this article, I want to address an issue with the last sentence, especially the use of the apply/with/let/also utility functions. Here their signatures as a reminder:

TODO apply/with/let/also signatures.

In my daily codebase, I used a lot. But it is important to have in mind that is not mandatory, and should not be used systematically whenever we can.  
Here I expose a brief list of when not abuse on  
Readability  
Conciseness

```kotlin
with(doSomething(myVariable).getResult()){
    member = if(this > 0) {
        createMember(this) 
    } else {
        newMember(this)
    }
}
```

When I see the code above, I get confused by the fact that I instinctively expect the variable `member` to be an actual member of `doSomething(myVariable).getResult()`, but I get stopped by this last and I ask me: What `doSomething(myVariable).getResult()` currently is? We may put this in a variable in order to give it a name.

```kotlin
val myResult = doSomething(myVariable).getResult()
with(myResult){
    member = if(this > 0) {
        createMember(this) 
    } else {
        newMember(this)
    }
}
```

Good! I have a `myResult` as `this` and now I know that I want a result to be a positive number to create an actual value. But sadly I continue seeing in my mind that still `member` being affected to `this` from the wrapping `with`. I understand the practicity of using with but I see the actually use in this case is just avoiding to name a variable.

Let change the code to:

```kotlin
val myResult = doSomething(myVariable).getResult()
member = if(myResult > 0) {
    createMember(myResult) 
} else {
    newMember(myResult)
}
```

Yes, I just get rid of the `with` because to me it does not add extra value to the readeability of the code, IMHO it was there just because it was easier to write.

From **Clean code**, Use Intention-Revealing Names:

> Choosing good names takes time but saves more than it takes. So take care with your names and change them when you find better ones. Everyone who reads your code (including you) will be happier if you do.

---

Of course in the snippet above `myResult`, as a simple example, does not reveal any concrete intention. I hope is enogh to point out the abuse of `with`in this case. Anyway if it does not, I leave here the comparion of the code that made me write this article:

The original code for an android app:

TODO jp code

In this code we see the same use of `with`. I

## Conclusion:

I feel 

Use a with just to avoid 

After writing a `with` remind if this is allowing to shorcut should is creating new behaviour. 