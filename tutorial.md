---
title: Tutorial
---

<div class="span14">

## Install

You can install from [Hackage][HAC], by using _cabal-install_.

    $ cabal update
    $ cabal install Peggy

Now, you can use Peggy!

## First Example

Here is first example.

    {-# Language TemplateHaskell, QuasiQuotes, FlexibleContexts #-}
    
    import Text.Peggy
    
    [peggy|
    nums :: [Int]
      = num*
    num ::: Int
      = [0-9]+ { read $1 }
    |]

    main :: IO ()
    main = print . parseString nums "<stdin>" =<< getContents

It is a parser which parses a sequence of integers.
Let's try it.

    $ cat input 
    1 2 3 4 5
    $ runhaskell Test.hs < input
    Right [1,2,3,4,5]

It seems to run correctly :)

## Language Extensions

At first line,

    {-# Language TemplateHaskell, QuasiQuotes, FlexibleContexts #-}

add these language extensions for using peggy embeded DSL.

## Import Peggy module

To use Peggy, you need simply to import Peggy module.

    import Text.Peggy

## Define a parser

Now, you can define your parser.
Peggy is embeded DSL (eDSL) style parser generator,
so you wrote your parser in a Haskell file directly.

Peggy is provided by Quasi-Quoter.
In order to define parser, write a syntax in quasi-quoter [peggy| ... |].

    [peggy|
    nums :: [Int]
      = num*
    ... define syntax here
    |]

A parser is a set of definitions. You can use full PEG syntax and Peggy extensions.
A full Peggy syntax is described [here](/syntax.html).

## Typical form of syntax

A typical form of syntax definition is

    <name> :: <Haskell-Type>
      = <expr> ... { <semantic-by-haskell-code> }
      / <expr> ... { ... }
      ...

Each definition has several alternatives,
and each alternative is a sequence of primitive expression with semantic.
Semantic is a single haskell expression with place holder.
It specify the result value of the nonterminal.

## Token

Peggy handles implicit token.
You can define a nonterminals as a token by using triple-colon (:::)

    num ::: Int
      = [0-9]+ { read $1 }

It allows any leading/trailing spaces.
For more detail about token behaviour, please refer to [syntax maual](/syntax.html#token).

## Generated parser

Peggy quasi-quoter generates a set of _parsers_.
Parser is defined for each nonterminal symbol has same name.

    nums :: Parser [Int] -- It is simplified. Actually, it has more complicated type.
    nums = ... (Haskell code generated by Peggy)

## Invoking parser

At last, you can invoke your parser by using following function.

    parseString :: ListLike str Char
                   => Parser a
                   -> String
                   -> str
                   -> Either ParseError a
    parseString parser inputName input =
      ...

It can parse any String-like sequence by passed parser.

## Learn more

* Some example of more complex parsers are [here](example.html).
* A full description of Peggy syntax is avaiable [here](/syntax.html).
* A self defined Peggy syntax file is [here][BOOT], it is used for bootstrapping peggy parser.
* A github repository is [here][REPO]. Any patch / pull requests are welcome!

</div>

[HAC]: http://hackage.haskell.org/package/peggy
[REPO]: https://github.com/tanakh/Peggy
[BOOT]: https://github.com/tanakh/Peggy/blob/master/bootstrap/peggy.peggy