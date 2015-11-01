+++
date = "2017-06-17T16:27:41-07:00"
title = "Types and Implicity"
toc = true
description = "A rumination on type systems and how different programming languages respond to attempts at implicit conversion."
keywords = [
  "type systems",
  "programming languages",
  "clojure",
  "ocaml",
  "haskell"
]
+++

## Trust:

The following is a rumination on some aspects of type systems and programming languages, along with several examples. I anticipate, only now just sitting down to write this, that the tone of the post shall be rather whimsical, although I could be wrong. It's possible that, after having completed the post and then reviewing it some before publishing, I shall discover that the tone is not whimsical. If that's the case, I may edit the post a bit, specifically these ruminations on anticipated whimsicalness, with the intent of not misleading the reader. It is important to mention that, by design, all edits to this post after it has been published shall be discoverable publicly, due to the source code for the site being [publicly available](https://github.com/maxdeliso/maxdeliso.github.io) alongside the generated website. Now that we have hopefully established some trust, let's prevaricate some more prior to veering into the more salient points.

### Target audience:

Normally these posts begin with an elaborate prelude warning the reader that they may not be able to cope with the material that is to follow, and that merely considering skipping forward into the details of the article below without having successfully defended a thesis shall cause blindness and difficulty breathing, at a minimum. Then, after that, a lengthy list of qualifications that are deemed acceptable as a substitute for successful thesis defense are supplied, in decreasing order of preference. I shall address both of these formalities as follows:

1. Keep reading.
2. I am going to explain a lot of things, so if you are kind of bored because you know a lot of the things that I'm explaining already then (per 1) keep reading all the way to the end and _then_ comment about it.
3. Consider the Mistakes section that follows.

### Mistakes:

My intent is to be correct here, but the topic of discussion is rather complex, and alas I am human, (merely a thin film of veins and gristle), and prone to error. I have done my best to not make any incorrect or misleading statements in the post, but I have also spent most of the post writing about terms that commonly evoke misunderstanding and disagreement (both of [these](https://en.wikipedia.org/wiki/Type_system) [pages](https://en.wikipedia.org/wiki/Strong_and_weak_typing) in Wikipedia are contentious at the time of this writing), so I anticipate that I will have been wrong at least once. Kindly do let me know if this has occurred, respectful discussion can only improve the quality of the post.

## Type Systems:

There are a lot of different programming languages with diverse characteristics, but a useful attribute for classifying programming languages is their type system, so what is a type system? Fundamentally, and by questionable analogy to natural languages, a type system is a means for classifying the nouns and verbs of a programming language in order to constrain what statements are legal. These constraints are most valuable for preventing errors from being introduced into programs written in the language. When programming computers, all you are ever doing is specifying exactly what computations you want performed, and you are writing text to explain - step by step - what the computations are. Type systems are there to make it harder to express operations on data that do not support them. Some languages are designed to leverage type systems extensively, while others barely emphasize types at all, though few practical languages completely lack the idea of types.

### Static vs Dynamic:

Static type checking verifies that the constructions in a program are validating according to the rules of the type system _before_ the program is executed. A failure to type in a statically typed language typically is reported as a compiler error. Dynamic type checking defers type checks until the program is already executing (usually this means that the language is interpreted, though the definition of interpreted is a little vague as well). Most all languages are hybrids with regards to how they handle types (as well as how they are executed), so it's difficult to make precise generalities about families of languages and where they fit on this spectrum.

### Note about "strong" vs "weak":

There is some colloquial use of the titular terms to express a continuum of type safety. I eschew these terms entirely here, due to their imprecision, preferring analysis of specific examples.

### Implicit conversion examples:

Often type system discussion is motivated by implicit conversion as the classic example of why static type systems are preferable to dynamic systems. A computer program is presented with an obvious error in it, and then there is some explanation of how a type system would have prevented the execution of the program containing the error, were it written in a statically typed language. In this post, a small useless program is presented with almost no context, and then translated to many languages. The intent of this post is to introduce ideas about types.

##### Javascript:

Javascript itself is not statically typed, there is no facility for declaring the type of your variables. This makes it easier to write short programs more quickly, but writing very large programs that are also correct becomes a difficult exercise indeed. In the example below, the "result" variable is assigned a string with the value of "cat", appended with the value of the "age", except coerced to a string. In this specific example, which has been contrived to demonstrate that a lack static type checking can lead to program constructions that lack useful meaning, the value of "result" doesn't have a useful meaning (but then neither does the program).

{{< highlight javascript >}}
var cat = "minkies";
var age = 2;
var result = cat + age; // "minkies2";
{{< /highlight >}}

Do not interpret this example as disparaging the design of Javascript, it is entirely possible that one could depend on this particular behavior to produce a succinct program that would otherwise have been longer and more cumbersome in a statically typed language. The dynamic nature of Javascript is regarded as a strength by its designers. There is a more complete exposition on Javascript's language design [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript#Overview). There are a number of languages that have static type systems that compile to Javascript, that also retain similar syntax to Javascript, the best known of which is probably [TypeScript](https://www.typescriptlang.org/index.html).

##### C:

Warning, this C may [put hair on your chest](https://english.stackexchange.com/questions/296311/using-put-hair-on-your-chest-for-women) (and potentially offend you unless your sense of humor is earthy and robust)!

{{< highlight c >}}
#include <stdio.h>

int main() {
  char cat [] = "minkies";
  int age = 2;

  printf(cat + age); // prints "nkies"
  return 0;
}
{{< /highlight >}}

At first glance, this result makes no sense, especially to someone that hasn't spent time learning the rules of C. What did happen was a lot of implicit conversion that lead to some surprising results - despite static type checks being performed by the C compiler. The "cat" variable was actually a [pointer to a character array](http://boredzo.org/pointers/#strings), and "age" was interpreted as the offset into that character array. The whole thing was printed as a string, due to the definition of printf. What we can learn from this is that the definition of a language and its type system are intermixed, and this can lead to surprising results if either are not understood.

##### Python:

As a point of comparison, let's look at another language that is not statically typed and see how it handles this particular case:

{{< highlight python >}}
cat = "minkies"
age = 2
cat + age
# TypeError: cannot concatenate 'str' and 'int' objects
{{< /highlight >}}

Python is not statically typed, but it will also not implicitly convert function arguments to different types. Python's [documentation](https://docs.python.org/2/reference/datamodel.html#objects-values-and-types) (for version 2) on the subject is extremely precise, and they mention there that "[an] object’s type determines the operations that the object supports [...] and also defines the possible values for objects of that type." So, static checking is not the only way to have useful type checks.

##### Java:

Java is statically typed. If you were waiting for the Java example here, expecting a triumphant statement of superiority, you're going to be disappointed by what follows: it will happily allow implicit conversion if the programmer is explicitly vague in a permissible way, as below.

{{< highlight java >}}
class Types {
    static final String cat = "minkies";
    static final int age = 2;
    // String works here too as the type of result, but int does not
    static final Object result = cat + age;

    public static void main(String [] args) {
	System.out.println(result); // prints "minkies2"

	Class<?> resultRuntimeClass = result.getClass();
	// "... java.lang.String"
	System.out.println("the runtime class of result is: "
				+ resultRuntimeClass.getName());

    }
}
{{< /highlight >}}

This is a little different because the programmer was allowed to specify Object as the type of the "result" variable, and the program was still executed. Java does perform an [implicit conversion](https://docs.oracle.com/javase/specs/jls/se8/html/jls-5.html#jls-5.1.11) here; the run time type of the result variable is String, whether its statically declared type is String or Object, and both alternatives are permissible by Java's static type checker. What we can learn from this is that static type systems can be permissible in the sense that they allow some constructions where the statically supplied type and the runtime type do not match.

##### Clojure:

In case you're not familiar with [Clojure](https://clojure.org/), it's a [LISP](https://en.wikipedia.org/wiki/Lisp_(programming_language)) dialect. According to the website, it's "a compiled language, yet remains completely dynamic," which makes it interesting for the purposes of this discussion. Let's see how it fares in the implicit conversion test:

{{< highlight clojure >}}
(let [cat "minkies" age 2] (+ cat age))
;; ClassCastException java.lang.String cannot be cast to java.lang.Number  clojure.lang.Numbers.add (Numbers.java:128)
{{< /highlight >}}

Not unlike Python in this instance, Clojure has determined a run time type for the two variables, and then discovered that the addition operator was not applicable to that pair, upon attempting to perform the application, ultimately terminating with an exception. According to its creator, Clojure does not place a lot of emphasis on the [use of types](https://youtu.be/ROor6_NGIWU?t=5m57s). Interestingly, a third party has [supplied optional types as a library](http://typedclojure.org/) within Clojure. The fact that this was even possible was due to the extreme flexibility of the Clojure language.

##### OCaml:

[OCaml](https://ocaml.org/) is a language with a novel type system.

Here's how it handles the example case:

{{< highlight ocaml >}}
let cat = "minkies";;
(* val cat : string = "minkies" *)
let age = 2;;
(* val age : int = 2 *)
cat + age;;
(* Error: This expression has type string but an expression was expected of type int *)
{{< /highlight >}}

Something else to notice in here is that the type of the variables were inferred and printed by the interpreter. The error that resulted was actually very useful in explaining why the code was not allowed to execute.

##### Haskell:

[Haskell](https://www.haskell.org/) is a language whose design is extremely influenced by type theory, and places more emphasis on types than most other languages. Like OCaml, it also supports type inference to remove the need to specify all of the types, while retaining the safety that types provide.

{{< highlight haskell >}}
let age = 2
let cat = "minkies"
cat + age
-- <interactive>: ... error:
--    * No instance for (Num [Char]) arising from a use of `+'
--    * In the expression: cat + age
--      In an equation for `it': it = cat + age
{{< /highlight >}}

The result of attempting to add two items with incompatible types on the REPL results in failure very similar to what was seen in OCaml, and for similar reasons. Haskell has a concept of [type classes](https://www.haskell.org/tutorial/classes.html) which groups together operations on types. The error message in this case is saying that it could not find how to make sense of an array of characters in terms of the [Num](https://hackage.haskell.org/package/base-4.9.1.0/docs/Prelude.html#t:Num) class, which describes numeric things.

## Trade-offs:

  Language      | Discipline | Result
  ------------- | ---------- | ------
  Javascript    | Dynamic    | "minkies2"
  C             | Static     | "nkies"
  Python        | Dynamic    | exception
  Java          | Static     | "minkies2"
  Clojure       | Dynamic    | exception
  OCaml         | Static     | compile error
  Haskell       | Static     | compile error

Many programming languages have different approaches to types, some of which have been captured here with simple examples. Effective use of types can enable the construction of larger more complex programs. The stricter a type system in a language is, generally the harder it is to learn to use, and the more restricted the composition of programs in that language is. Types are often used to more rigidly define a shared context to reason about programs with. I believe that the level of type safety that should be applied in a given domain should be decided by the penalty for failure in that domain.

## Further Reading:

There is a lot to know about types. [This book](https://www.cis.upenn.edu/~bcpierce/tapl/) is perhaps the defacto book of types and their theoretical basis. Finally, types are far from the only mechanism for ensuring program safety.