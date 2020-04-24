---
title: "Readability vs maintainability"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - java
---

Let's dive into some typical code seen in most projects. Here's a sorting algorithm written in Java.

```java
public class Sorting {
    public static boolean sort(int n, int[] array) {
        if (n == 1) {
            return isSorted(array);
        } else {
            // Use Heap's algorithm
            for (int i = 0; i < n - 1; i++) {
                if (sort(n - 1, array)) {
                    return true;
                }

                if (n % 2 == 0) {
                    swap(array, i, n - 1);
                } else {
                    swap(array, 0, n - 1);
                }
            }
            return sort(n - 1, array);
        }
    }

    static void swap(int[] array, int i, int j) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }

    static boolean isSorted(int[] array) {
        for (int i = 1; i < array.length; i++) {
            if (array[i] < array[i - 1])
                return false;
        }
        return true;
    }
}

```

While this code executes just fine and is readable, there are quite a few improvements to be made for those who strive for perfection.

# Naming conventions

Instead of focusing on exactly what a function or variable does, remember to think about its purpose also. For example, the function `isSorted` is used only to check whether the algorithm has succeeded, therefore a better name is `isOk`. It is also shorter.

Local variables are obvious candidates for code length optimization. For example, there is no reason to have a full 5 bytes for `array`. It could simply be called `a`.

## Variable naming

In the past, some languages didn't allow long variable names or, in the case of [TRS-80 Level 2 Basic](http://www.trs-80.org/level-2-basic/) only the first 2 letters of a variable name were significant. For example, `VAT` and `VALUE` would mean the same thing.

When compilers & linkers started allowing longer variable names, people quickly begun to overuse this without thinking of its consequences. This is a good thing when used with caution, but beware of the following issues:

* The program's size can quickly expand and become unmaintainable
* When porting the code to some languages, name clashes might occur
* When sent over the web, such as JavaScript, the code will take longer to load
* The names can't be reused

### Name re-use

If the variables have generic names, such as `data`, `a` and `i` then you can re-use these names without declaring new variables. An additional bonus of this is that you reduce the stack usage, minimizing the risk for stack overflows.

### Learn by example

A good example of a great balance between name length and readable semantics is the naming conventions of the [BLAS](http://www.netlib.org/blas/blasqr.pdf) library. Thanks to one of our early readers for pointing this out!

# Commenting

The comment about Heap's algorithm is obvious for those who already know said algorithm and thus redundant. For those who don't, however, the comment only causes confusion and is therefore just a distraction. So, it doesn't really help anyone.

Helpful comments are those who describe code segments in such a way that the reader understands what the code does without reading it. So, in the above example the swap function could do with a comment as it's written quite verbosely. Furthermore, it could be rewritten in the standard way, the [XOR swap](https://en.wikipedia.org/wiki/XOR_swap_algorithm):

```java
static void swap(int[] a, int i, int j) {
    a[i] ^= a[j] ^= a[i] ^= a[j];
}
```

When written like this, no comment is necessary. It's important to stick to standard patterns so the code is self-documenting.

# Early returns

Always return as early as possible, to make sure you stay on the lowest indentation level possible. The original code is unnecessarily complicated as it contains an `else` block for no reason.

# Final version

Using the improvements mentioned above, you can now see how we achieved a good balance between readability and maintainability!

```java
public class Sorting {
    // Sort the array "a"
    public static boolean sort(int n, int[] a) {
        if (n == 1) return isOk(a);
        for (int i = 0; i < n - 1; i++) {
            if (sort(n - 1, a)) return true;
            swp(a, n % 2 == 0 ? i : 0, n - 1);
        }
        return sort(n - 1, a);
    }

    // Swap two values in the array "a"
    static void swp(int[] a, int i, int j) {
        // Workaround for XOR swap in Java
        int t = a[i]; a[i] = a[j]; a[j] = t;
    }

    // Return true if "a" is OK
    static boolean isOk(int[] a) {
        for (int i = 1; i < a.length; i++)
            if (a[i] < a[i - 1])
                return false;
        return true;
    }
}
```
