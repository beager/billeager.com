---
title: New Language Features in Idiomatic Scud 2.13a2
date: "2020-04-10"
description: "An in-depth look at what's new in the latest version of Scud."
cover_image: "./scud.jpg"
---

![](./scud.jpg)

The [Scud language](http://scud-lang.lan) is a beautiful, idiomatic, perfect programming language, and I refuse to work in any other language. This presents some minor issues at my current gig, where the mouth-breathers that I work with develop only in Go, JavaScript, and some Ruby. They have sadly not been introduced to the glorious simplicity of Scud's compile-time SIMD optimization, enhanced subsystem garbage collection, and prohibitive learning curve.

The latest [2.13a2 pre-release](http://scud-lang.lan/releases/2.13a2) of the Scud language provides a lot of exciting new features for concurrent programming that you probably could never understand, and is a major upgrade from version 2.12.7. I was having a conversation with a coworker this morning about those new features, and after about ten minutes of telling him about the runtime optimizations of the new features in the nightly build, he said "huh, cool" and walked away. I went back to my desk, where I continued neglecting two sprints worth of outstanding dev work to resume tinkering with the awesome new features of Scud, brainchild of the impossibly brilliant [Karlhans Schmidtvelt von der Schmusselhogan](http://scud-foundation.lan/about/karlhans), founder and benevolent-dictator-for-life of the [Scud Language Foundation](http://scud-foundation.lan).

Consider the following Scud 2.12.7 code that I wrote while I should have been paying attention in today's 10AM team meeting:

```clike
dfn absolute<>[,,] Converged::abs concurrentFunc (impl static funcOne, impl static funcTwo)& :: is<>
    jf:any nDefn <= subproc a* (funcOne as kFuncOne) >> { eval? kFuncOne : kFuncOne.res or any(Error) }
    jf:any nDefn <= subproc b* (funcTwo as kFuncTwo) >> { eval? kFuncTwo : kFuncTwo.res or any(Error) }
    sync a+b*<?jf:any[]> resProducer <= do!
        lmb x[x=>x]* <= x^>x % x
            x && x
        od!
    coalesce nDefn[a+b**] > a* > b*
    expl return ReturnValue[as&lt;Converged::abs&gt;](&a, &b)!!
endfn
```

Easy enough, right? This code is pipe-safe, computably agnostic, converges predictably, and provides enough flexibility for almost any use case you can contrive.

After the meeting my tech lead came up to me and asked if I had any idea why our last four production builds failed. As you can see above, Scud removes those problems with its elegant approach to managing concurrency. But the new features in 2.13a2 make it even easier to concurrently synchronize nonconvergent or semiconvergent functions. Consider the following code that I wrote using Scud 2.13a2's new language features, while I should have been reviewing pull requests for our quarterly legacy system release this Friday:

```clike
dfn absolute&lt;Converged&gt;[,,] concurrentFunc (impl[] static funcList..)& :: is<>
    sync [a*.._*] resProducerFactory(&lt;?jf:any[]&gt; lmb x=>x!!) <= do!
        lmb virtual nDefn <= subproc shadow .._* (absFunc as kAbsFunc) >> { eval? kAbsFunc : kAbsFunc.res or any(Error) }
        od!
    coalesce nDefn[a*.._*] > ...funcList &
    expl return ReturnValue[as&lt;Converged&gt;[]](&&funcList)!!
endfn
```

I was awestruck at the simplicity of the new language features. The addition of `lmb virtual` and coalescence destructuring makes variable size matrix refunctionalization easy. As I glanced over an email from my director about a meeting at 3pm, I began to dig into the release notes from Scud 2.13a2 to see what other neat features to expect when Scud 2.13 gets its general availability release. Consider the following:

<blockquote>
New <kbd>lmb</kbd> keywords in Scud 2.13:

- `lmb virtual`: Refunctionalizes matrices using quasi-process virtualization. Suitable for materialized or anticipated variable-size matrices.
- `lmb impl virtual`: Refunctionalizes matrices using implicit-process virtualization. Not suitable for materialized static or variable-size matrices.
- `lmb evented virtual`: Refunctionalizes matrices using reactive process optimization. Suitable for any `PrimitiveEvented` subclassification.
- `lmb lmb`: Pointer to lambda refunctionalization primitives, for pipelined introspection.
  </blockquote>

I'm especially excited about `lmb evented virtual` and I'll be looking for any excuse to use it in an upcoming project. I plan to have a more in-depth blog post about the new `lmb` keywords on the blog soon!

One great thing about the new language features, especially concurrent function argument destructuring, is that it makes transpiling JavaScript to Scud a lot faster. Rather than having to overload concurrent function signatures, you can defer destructuring of concurrent function arguments and resolve them during the refunctionalization virtualization pipeline. This was the result of applying Scud 2.13a2's features to my JS to Scud transpiler:

```sh
grunt@I-Hate-Work legacy-system % scud-run ./js2scud-2.12.7.scud -R --inplace -i ./src/**/*.js -o ./src/**/*.scud
js2scud - JavaScript to Scud 2.12.7 converter

Generating ingest manifest ......... done
Generating output plan ........ done
Converting files .....................................
......................................................
......................................................
......................................................
......................................................
......................................................
................................................. done

Converted 42,677 files in 2h43m15.411s

grunt@I-Hate-Work legacy-system % scud-run ./js2scud-2.13a2.scud -R --overwrite --inplace ./src/**/\*.js -o ./src/**/\*.scud
js2scud - JavaScript to Scud 2.13a2 converter

Generating ingest manifest ......... done
Generating output plan ........ done
Converting files .....................................
......................................................
......................................................
......................................................
......................................................
......................................................
................................................. done

Converted 42,677 files in 2h24m58.905s

grunt@I-Hate-Work legacy-system % git checkout master && git add . && git commit -m 'wip' && git push --force
```

As you can see, the new language features make transpiling almost **12% faster**. As my director went on in our 3pm one-on-one meeting about team players and expectations, I couldn't help but marvel at how the improvement to my conversion script brought on by a few simple function argument destructuring optimizations exceeded every single one of _my_ expectations. "Are you listening?" my director asks. _Oh yes. I'm listening, and I hear the blazing fast glory of Scud loud and clear._

There are a few other changes to the Scud runtime that are appealing in 2.13a2. Symbol deduplication has been improved, and dynamic overlays have been retooled to enable just-in-time proto-checking. How many times have you written the following:

```clike
impr *{} of "proto.sch" take [checkProto]

expr dfn concrete<> Void entry (arg#, @arg)& :: is<>
    glob glob <=*^^ checkProto!!

    # .. your code

    unglob glob <=*
    expl return Void!!
endfn
```

Imagine never having to do that again. It's incredible how Karlhans Schmidtvelt von der Schmusselhogan and the whole Scud Language Foundation maintain their relentless dedication to making a perfect language even more beautiful. At 5PM, my director came over with someone I'd never met before and asked if I could meet in Conference Room C, undoubtedly to discuss more in depth about my proposal to rewrite our legacy monolith in Scud that I emailed to the entire engineering team at 2AM last Sunday.

Scud 2.13a2 even introduces a few new operators that will undoubtedly be useful for building more elegant pipelines. From the release notes:

<blockquote>
New pipeline operators in Scud 2.13:

- `?!`: uncloak future operator, unwraps future indeterminate values in retroactive pipeline stages
- `**^`: dereference union operator, bitwise AND applied to dereferenced pipeline values
- `<<~>>`: continuum virtualize operator, virtualizes left and right values in predicate pipeline stages
- `(!)`: transient not operator, conditional bitwise NOT applied when operator congruency is determined in future pipeline stages
  </blockquote>

A lot of people are intimidated by how fast the Scud core dev team is moving to introduce new features into the language. I personally am grateful for their pace of innovation, and look forward to updating my hobby repos to utilize new language features any time those new features get added. In my meeting, as my director handed me my belongings in a box, my head was swimming in fascination at how you get everything right out of the box with Scud: static concurrency guarantees, syntax interpolation, an intuitive five-layer interpreter opcache, and a supportive community of dozens of passionate Scud developers around the world.

Anyway, I hope you've enjoyed this exploration of the new features of Scud 2.13a2. As it so happens, I'm in the market for a new gig. If you're solving any interesting cutting-edge technology problems with Scud 2.13a2 or later, feel free to email me.
