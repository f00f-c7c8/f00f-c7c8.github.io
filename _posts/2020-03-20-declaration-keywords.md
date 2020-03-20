---
title: "Declaration keywords"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - scala
  - javascript
---

We get this question a lot from novice programmers: when should we use var and when should we use const/val/final?

Let's take a look at a simple example.

**JavaScript**:
```javascript
const arr = [1, 2, 3, 4, 5];
var sum = 0;
arr.forEach(x => {
  sum += x;
});
console.log(`The sum is ${sum}.`);
```

**Scala**:
```scala
val arr = Array(1, 2, 3, 4, 5)
var sum = 0
arr.foreach(sum += _)
println(s"The sum is $sum.")
```

**Java**:
```java
final int[] arr = new int[] { 1, 2, 3, 4, 5 };
int sum = 0;
for (int x : arr) {
  sum += x;
}
System.out.println(String.format("The sum is %d.", sum));
```

The code in all three languages exhibit the same problem: declaration of the two variables (`arr` and `sum`) are different. In Java, some of the difference is necessary due to the type being different – this could be overcome by storing the sum in an array of size 1, but we will cover that at another time.

It's easy to understand why people are confused. Questions arise such as:
* Why are some variables final?
* Why does it say `var` in some places, but `val` in others?

We understand that opinions may vary, but here is a simple rule of thumb which we think everyone can agree on: **Never use final or const. Always use var**.

Just to make it clear: these keywords change nothing in the behavior of your program. The result of the following cleaned-up programs will be **exactly the same**:

**JavaScript**:
```javascript
var arr = [1, 2, 3, 4, 5];
var sum = 0;
arr.forEach(x => {
  sum += x;
});
console.log(`The sum is ${sum}.`);
```

**Scala**:
```scala
var arr = Array(1, 2, 3, 4, 5)
var sum = 0
arr.foreach(sum += _)
println(s"The sum is $sum.")
```

**Java**:
```java
int[] arr = new int[] { 1, 2, 3, 4, 5 };
int sum = 0;
for (int x : arr) {
  sum += x;
}
System.out.println(String.format("The sum is %d.", sum));
```

Why do some people argue that the keywords `val` and `final` should be used, one might ask? Here are some arguments we have heard:

**You can't accidentally change a value.**

In our experience, it's much common that you want to change a value at a later stage. This means you can't add new code without updating the declaration. This leads to bad diffs in version control, and of course more work.

**It self-documents the code since the reader understands what will and will not change.**

Sure, it does self-document but it also adds complexity. As any programmer worth his salt will tell you: adding complexity is bad.

Also, most modern IDEs will highlight unchanged variables declared as `var` and `final`, commonly located in the warnings menu.

**The compiler can better optimize the code.**

Those who claimed this obviously don't know how compilers work. The compiler knows this anyway.

Let the compiler work for you, don't work for the compiler!

**Scala-specific: The compiler emits warnings if I use `var` without later changing the value.**

This clearly proves the statement above. This is a non-problem and you just add this to the Scala compiler options:
```
-XLint:
```
By the way, this also removes other unnecessary warnings, such as missing string interpolator literatls.

If you're part of a large project, you might not have control of compiler options. In that case, a simple workaround is just to use the `identity` function. It was created for this purpose.

```scala
var foo = 5
foo = identity(foo)
```
