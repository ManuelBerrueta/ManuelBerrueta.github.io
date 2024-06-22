---
title: "Functional Programming With Haskell"
date: "2019-09-23"
coverImage: "1200px-Haskell-Logo.svg_.png"
---

The purpose of this blog is to share some Haskell resources and a brief overview of some Haskell concepts.

If you have written code before one of the key things is that in Haskell, variables are immutable, that is, once declared they cannot be reassigned. e.g. Given x=2 you cannot reassign it later to x=3 per say.

The language is similar to Python in that indentation matters and control of the program is executed that way, but its a little quirky so read [here](https://en.wikibooks.org/wiki/Haskell/Indentation) for further information.

It is also the case that we don't have loops, that means that we have to, you guessed it, use recursion! That along with "helper" functions and other functions to help us achieve our goal.

The best thing about it all is that it shows you how to solve problems in a different way, which I really like!

Below I have put some of the features of Haskell as well as some resources to help you learn and clarify concepts.

#### Tail Recursion

In Haskell the use tail recursion is treated differently and the compiler optimizes for this and allows the same stack space to be used, instead of building up a stack. That is, if you have ever used recursion on large data sets, this means no stack over flow =)!

#### Higher Order Functions

A higher order function is a function which applies another function to a variable such a list. It take as one of its parameters another function or it can also return a function.

A great example if s the function map which takes a function applies that function to every item on a list, and returns that new list that has been modified.

#### Currying

Currying is where you use a function that takes other arguments and you call it with one argument. When this happens, the compiler will store that function with the passed argument and return a function demanding the rest of the arguments.

Some currying help:  
[https://stackoverflow.com/questions/6652234/how-does-currying-work](https://stackoverflow.com/questions/6652234/how-does-currying-work)  
[https://dev.to/nestedsoftware/currying-in-haskell-with-some-javascript-4moj](https://dev.to/nestedsoftware/currying-in-haskell-with-some-javascript-4moj)

### Resources

[http://learnyouahaskell.com/](http://learnyouahaskell.com/syntax-in-functions)  
[http://learn.hfm.io/](http://learn.hfm.io/)  
[https://mmhaskell.com/](https://mmhaskell.com/)  
[https://en.wikibooks.org/wiki/Haskell/Control\_structures](https://en.wikibooks.org/wiki/Haskell/Control_structures)  
[https://wiki.haskell.org/If-then-else](https://wiki.haskell.org/If-then-else)  
[https://hoogle.haskell.org/](https://hoogle.haskell.org/)  
[https://youtu.be/cTN1Qar4HSw](https://youtu.be/cTN1Qar4HSw)  
[https://stackoverflow.com/questions/33316533/whats-the-difference-between-the-data-and-type-keywords](https://stackoverflow.com/questions/33316533/whats-the-difference-between-the-data-and-type-keywords)
