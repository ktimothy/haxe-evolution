# Generic build macro for functions

* Proposal: [HXP-NNNN](NNNN-filename.md)
* Author: [Timothy Kovalev](https://github.com/ktimothy)

## Introduction

This feature allows to build generic functions body using macros,
just like `@:genericBuild` for classes.

## Motivation

Haxe already has `@:genericBuild`, which is very useful when you need to
execute different code for different concrete type parameters. The problem is
@:genericBuild creates new type, which is not always needed.

Haxe also has `@:generic` for functions. Unfortunately, it does not allow
to write some type-specific code, which is what I want.

I suggest to make it possible to define the body of function per each
concrete type parameter T.

Similar effect can be achieved using `macro` functions, but in case of `macro`
functions concrete type T is not always known (e. g. calling macro function
from abstract).


## Detailed design

First, we add a `@:genericBuild` meta on a function, just like we do for classes:

```
class Example
{
    @:genericBuild(Macro.build())
    public function buildMePlease<T>(arg1:T, arg2:Int):T {}
}
```

Then, we define a static function, that will do the stuff:

```
class Macro
{
    static public function build(context:Expr, arg1:Expr, arg2:Expr):Expr
    {
        return macro { trace('hi'); };
    }
}
```

Note that its arguments are very similar to `macro` functions: it gets context and
arguments expressions, and returns function body expression.

We definitely could make it more like `macro` functions by placing macro code into
the @:genericBuild function definition, but I think it is better to have this feature
more similar to class generic build.

## Impact on existing code

The feature should not have side-effects on existing code, should not break compilation.
Currently compiler ignores @:genericBuild on functions.

## Drawbacks

The only drawback I see is that this feature is like `macro` functions - it does almost
the same in some (bot not all!) cases.

## Alternatives

The alternative is to use @:genericBuild to generate classes instead of methods.
It is bad solution, because increases complexity of code and makes it more
difficult to understand.

## Opening possibilities

## Unresolved questions
