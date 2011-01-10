---
layout: post
title: There Are Always Options
location: Nashville
---

I'll be honest. I have a strong dislike for null references. They cause bugs. They require constant vigilance. In a well-written system, a significant amount of effort is expended protecting your methods from them. I'd abolish them entirely if I could. An opposing force is the constant need to return sensible values when no valid result can be constructed or indicate  no parameter value. [Code contracts][contracts] (in the .NET ecosystem) can help with enforcement of assumptions, but they don't solve the requirements that null sometimes fulfills.

Last year, I satisfied a long-time curiosity about functional languages by learning F#. During my adventures there, I found a reasonable solution. F#, like its more pure brothers, contains algebraic data types called [discriminated unions][]. One fundamental discriminating union provided by the F# designers is [option][fsharpoption]. Briefly, option is a generic that can either contain a value or not. As a generic, it can wrap an instance of any type. You _must_ handle the case of a missing value when working with options.

Most .NET shops are not using F# at this point.  Even if they were, some application domains do not lend themselves to a pure functional implementation. The F# option is available to all CLR languages, but has an unwieldy API. So, I ported the idea to C#. I've now used Option on multiple projects in multiple domains and have found it to be a wonderful alternative to null. It's [out there][options] now, for anyone to use. This is a primer.

## Option&lt;TOption&gt;

This structure is the heart of the library. It is implemented as a value type to remove the possibility of an instance being null. There are two constructors. One takes a value of TOption, one takes no parameters. If the first constructor is used, the instance will have a value (Some, in F# terms). If the second is used, no value will be contained (or None).

This structure has an extremely limited set of functionality defined. The design focuses on simplicity for the sake of reliability. Other than the functions needed to support equality comparison, only one function is defined, but it is a beautiful function and deserves its own section.

## Handle

Handle is very simple, but enforces the core principle that you must anticipate both option cases, there either is a value or there isn't. Handle takes 2 arguments, a delegate that takes an argument of the encapsulated type, returning a result, and a delegate that takes no arguments and returns a result of the same type. You must go through Handle to get to the value contained in the option instance.

## Option

Option is a static class containing methods that assist in the creation of Option&lt;TOption&gt; instances. These methods include:

- AvoidNull — takes a reference type instance and returns an option guaranteed to not contain a null reference.
- None — always returns an empty (None) option.
- Guard — Takes functions and returns a function that will never return null.

## OptionExtensions

Using raw options can be unwieldy in a (mostly) imperative language like C#. Working with functional arguments isn't familiar to everyone and you'll find a lot of repetition. OptionExtensions provides extension methods that go a long way toward easing the pain. There is a lot of functionality here, and this is the area of the project most likely to grow over time.

- GetValueOrDefault — returns the value contained in the option or a default value.
- GetValueOrThrow — returns the value contained in the option or throws an exception.
- Transform — allows you to work with the option value while maintaining the integrity of the encapsulation.

There's a lot more in OptionExtensions, and anyone using the library should really take a look here.

I've submitted the package for inclusion in the NuGet feed. If you use it, I hope you're happy with it. Let me know [@davidsidlinger][twitter].

[contracts]: http://research.microsoft.com/en-us/projects/contracts/
[discriminated unions]: http://en.wikipedia.org/wiki/Tagged_union
[fsharpoption]: http://msdn.microsoft.com/en-us/library/dd233245.aspx
[options]: http://github.com/davidsidlinger/options
[twitter]: http://twitter.com/davidsidlinger