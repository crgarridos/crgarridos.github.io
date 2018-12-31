---
layout: post
title: Kotlin - sugar abuses
description: Sometimes the syntactic sugar of kotlin can tempt us to write code less readable in some ways
tags: 
    - Kotlin
    - Clean code
    - Scope functions
    - Conventions
---

As widely known, Kotlin introduced very delightful features and utils functions in its standard library. Very often improving our productivity in crafting new code. <!-- more -->However, as more as we use these features, sometimes we can feel tempted to use them more and more. Personally, I think in all the cases when I wanted to get rid of extra lines, extra variable declarations, extra branching conditions and so on in order to read code in a more concise and compacted way. 

I want to address an issue with the last sentence above, specifically, the overuse of the scoping functions `also`, `apply`, `let`, `run` and `with`.

```kotlin
inline fun <T, R> with(receiver: T, block: T.() -> R): R {
    return receiver.block()
}

inline fun <T> T.also(block: (T) -> Unit): T {
    block(this)
    return this
}

inline fun <T> T.apply(block: T.() -> Unit): T {
    block()
    return this
}

inline fun <T, R> T.let(block: (T) -> R): R {
    return block(this)
}

inline fun <T, R> T.run(block: T.() -> R): R {
    return block()
}
```

In my daily codebase, I used a lot. But it is important to have in mind that it is not mandatory and they are different. While the [scope function conventions][scope-conventions] are not strict enough to make a difference in the usage of them. These should not be used systematically whenever we can. 

In this article I want to point a few of, I consider, bad usages of `with` in particular. But these could be extrapolated to other scope functions too.
Let say we have the following example:


```kotlin
with(doSomething(myVariable).getResult()){
    member = if(this > 0) {
        takeMember(this) 
    } else {
        newMember(this)
    }
}
```

At the first look, it would seem fine. But, when I try to actually understand what the code above does, I get confused by the fact that I instinctively expect the variable `member` to be an actual member of `doSomething(myVariable).getResult()`.  
Is `member` suitable to be either `this@with.member` or `this@OuterThis.member`?  
No way to know it without using an IDE or having the full code. In order to follow up, let say `member` is equivalent to say `this@OuterThis.member`.
Once this clarified, Could we answer what `doSomething(myVariable).getResult()` currently is? 

We may put this in a variable in order to give it a name.

```kotlin
val myResult = doSomething(myVariable).getResult()
with(myResult){
    member = if(this > 0) {
        takeMember(this) 
    } else {
        newMember(this)
    }
}
```

Good! I have a `myResult` as `this` and now I know that I want this to be a positive number to "take or create a member". But dismally I continue seeing in my mind that still `member` being affected to `this` from the wrapping `with`. I understand the habitude of using `with` but in this case, the actual use is just avoiding to name a variable.

Let change the code to:

```kotlin
val myResult = doSomething(myVariable).getResult()
member = if(myResult > 0) {
    takeMember(myResult) 
} else {
    newMember(myResult)
}
```

Yes, I just get rid of the `with` because to me it does not add extra value to the readability of the code, IMHO it was there just because it was easier to write.

From **Clean code** by *Uncle Bob*, Use Intention-Revealing Names:

> Choosing good names takes time but saves more than it takes. So take care with your names and change them when you find better ones. Everyone who reads your code (including you) will be happier if you do.

We could argue about the meaningless of `myResult`. Off course in the snippet above `myResult`, as simple example, it does not reveal any concrete intention. But I hope is enough to point out the abuse of `with` in this case. I also admit that `doSomething(myVariable).getResult()` is an exaggeration to impose making to declare a variable of out it. In simpler cases `myVariable.getMember()` or `explicitResult(myVariable)` could be explicit enough to do not declare an extra variable. However, consider that a well-named variable avoids mental mapping and improve the actual reading of the code.

## Conclusion:

I like to say, use `with` as a shortcut when you have not the intention of writing new values into itself or its members. Other way said:
- Use `with` just to avoid typing `myResult`. Like this, we can implicitly get access to its member without tapping `this`.
- Do not use `with` to modify the members of its receiver. In that case use `.apply` instead.
- Preferably, do not use `with` when interferes with the local scoped `this`.
- Take care the receiver passed to `with` has enough context to know in what the block is actually operating on.

**Related readings:**
- [Copying with kotlin's scope functions][s1m0nw1] *by Simon Wirtz* in kotlin expertise
- [Kotlin scoping functions][fatih] *by Fatih Coşkun* in medium
- [Clean Code][clean-code] - Chapter 2, *by Robert C. Martin*
- [Kotlin official conventions][conventions]

[scope-conventions]: https://kotlinlang.org/docs/reference/coding-conventions.html#using-scope-functions-applywithrunalsolet
[conventions]: https://kotlinlang.org/docs/reference/coding-conventions.html
[fatih]: https://medium.com/@fatihcoskun/kotlin-scoping-functions-apply-vs-with-let-also-run-816e4efb75f5
[clean-code]: https://www.oreilly.com/library/view/clean-code/9780136083238/
[s1m0nw1]: https://kotlinexpertise.com/coping-with-kotlins-scope-functions/