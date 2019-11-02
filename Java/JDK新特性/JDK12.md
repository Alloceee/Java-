Some of the great benefits you can expect from the new Java 12 features are:

- it will make the coding process easier by extending the switch statement and enabling it to be used as statement or expression
- boost the JDK build process by creating a class data-sharing archive through a default class list on the 64-bit platform
- reduce the garbage collection pause times by processing evacuation work while running the Java threads, which means that pause times are consistent regardless of the heap size
- add a suite of microbenchmarks into the JDK build source code–streamlining the running of existing benchmarks and the creation of new ones
- eliminate any duplicate work necessary with maintaining two port
- make aborting the garbage collection process much more efficient by breaking up the mixed collection group into mandatory and optional parts
- upgrade the G1 garbage collector to instantly return unused Java heap memory to the operating system when idle

### Java 12 features

- Switch expressions (JEP 325)
- Default CDS archives
- Shenandoah
- Microbenchmark suite
- JVM constants API
- One AArch64 port, not two
- Abortable mixed collections for G1
- Promptly return unused committed memory from G1

### Switch expressions (JEP 325)

With Java 12, the [beta switch expressions](https://openjdk.java.net/jeps/325) will improve coding by extending the switch statement, enabling its use as either a statement or an expression. It will let both forms use either the traditional or simplified scoping and control flow behavior. This will help simplify the code and also pave the way for the use of pattern matching in a switch.

Java developers are enhancing the Java programming language to use pattern matching to resolve several issues with the current switch statement. This includes: default control flow behavior of switch blocks, default scoping switch block (a block considered as a single scope) and switch working only as a statement.

In Java 11, the switch statement tracks C and C++ programming languages, and by default, uses the fall-through semantics. While the traditional control flow is beneficial when writing low-level codes, the error-prone nature will soon outweigh its flexibility as the switch is adopted in higher-level contexts.

Java 11

```java
int numberOfLetters;

switch (fruit) {
case PEAR:
    numberOfLetters = 4;
    break;
case APPLE:
case GRAPE:
case MANGO:
    numberOfLetters = 5;
    break;
case ORANGE:
case PAPAYA:
    numberOfLetters = 6;
    break;
default:
    throw new IllegalStateException (“Wut” + fruit);
}
```



Java 12

```java
int numberOfLetters = switch (fruit) {
    case PEAR -> 4;
    case APPLE, MANGO, GRAPE -> 5;
    case ORANGE, PAPAYA -> 6;
}
```

### Default CDS archives (JEP 341)

The end goal is to improve the JDK build process by generating a class data-sharing (CDS) archive with the help of the default class list on the 64-bit platform, effectively removing the need to run <code>java -Xshare:dump</code>. Among the goals for this feature are: 1.) Improve out-of-the-box startup time, and 2.) Get rid of the need to run -Xshare: dump to benefit from the CDS.

### Shenandoah: A low-pause-time garbage collector (JEP 189)

Shenandoah is a garbage collection (GC) algorithm that aims to guarantee low response times (the lower end of 10 – 500 ms). It reduces the GC pause times by doing evacuation work simultaneously with the running Java threads. With Shenandoah, pause times are not dependent on the heap’s size. This means that pause times will be consistent no matter the size of your heap. A 10 MB or 10 GB heap should have the same pause time.

This is an experimental feature and is not included in the default (Oracle’s) OpenJDK build.

### Microbenchmark suite (JEP 230)

This feature adds a suite of microbenchmarks (approximately 100) to the JDK source code and simplifies both the running of existing microbenchmarks and the creation of new ones. It is based on the [Java Microbenchmark Harness (JMH)](https://openjdk.java.net/projects/code-tools/jmh/) and supports JMH updates.

This feature makes it simple for developers to run current microbenchmarks and add new ones to the JDK source code. This will easily test the JDK performance based on the [Java Microbenchmark Harness (JMH)](https://openjdk.java.net/projects/code-tools/jmh/). It will support JMH updates and include an initial set of approximately 100 benchmarks in the suite.

### JVM constants API (JEP 334)

JEP 334 introduces an API modeling the key class-file and run-time artifacts, such as the constant pool. This API will include classes such as ClassDesc, MethodTypeDesc, MethodHandleDesc, and DynamicConstantDesc. A draft snapshot of the API is available [here](https://cr.openjdk.java.net/~vromero/constant.api/javadoc.04/java/lang/invoke/constant/package-summary.html). This API can be helpful for tools that manipulate classes and methods.

### One AArch64 port, not two (JEP 340)

Instead of two ports, Java 12 will only have one port for the ARM 64-bit processors (aarch64). The objective is to delete all arm64 port-related sources, while keeping the 32-bit ARM port and 64-bit aarch64 port.

This will shift the focus to a single 64-bit ARM implementation and eliminate duplicate work needed to maintain two ports. There are two 64-bit ARM ports in the current JDK 11 implementation.

### Abortable mixed collections for G1 (JEP 344)

This feature makes the G1 (Garbage First) garbage collector abort the garbage collection process more efficiently by splitting the mixed collection set into mandatory and optional parts. By allowing the garbage collection process to prioritize the mandatory set, G1 can meet the pause time goal more often.

G1 is a garbage collector designed for multi-processor machines with large amounts of memory. Due to its increased performance efficiency, the G1 garbage collector will eventually replace the CMS (Concurrent Mark Sweep) garbage collector. You can read more about garbage collectors [here](https://stackify.com/what-is-java-garbage-collection/).

One of the main goals of G1 Garbage Collector is to meet a user-supplied pause time target for the collection pauses. The G1 adopts an analysis engine that selects the amount of workload to process during a collection. The outcome of this selection process is a set of regions known as *collection set*. As soon as the collection set is established and the collection has started, then G1 will collect all live objects in collection set’s regions without stopping.

If G1 finds out that the collection set selection selects the wrong number of regions repeatedly, it will switch to an incremental way of processing mixed collections set by splitting the collection of to-be garbage collected regions into 2 parts – mandatory and optional parts. Then cease the garbage collection of the optional part, if the pause time target is not reached.

### Promptly return unused committed memory from G1 (JEP 346)

The main goal for this feature is to improve the G1 garbage collector to immediately return Java heap memory to the operating system when inactive. To achieve this goal, G1 will–during low application activity–periodically generate or continue a concurrent cycle to check the complete Java heap usage.

This will trigger it to immediately return unused Java heap portions to the operating system. When under user control, there’s an option to perform a full GC to maximize the volume of memory returned.

### Raw string literals (JEP 326) removed from JDK 12

Raw string literals was proposed as a preview feature of Java 12, but was later dropped. Its future release is currently postponed and it is being revised. [Here’s a quote from Brian Goetz](https://mail.openjdk.java.net/pipermail/jdk-dev/2018-December/002402.html), Oracle’s Java Language Architect, on December 11, 2018:

> While we can expect that for any language feature, there will be a 
> nontrivial volume of “I would have preferred it differently” feedback, 
> in reviewing the feedback we have received, I am no longer convinced 
> that we’ve yet got to the right set of tradeoffs between complexity and 
> expressiveness, or that we’ve explored enough of the design space to be 
> confident that the current design is the best we can do. By 
> withdrawing, we can continue to refine the design, explore more options, 
> and aim for a preview that actually meets the requirements of the 
> Preview Feature process (JEP 12).
>
> — Brian Goetz

### JDK 12 early access builds – Try it here!

The JDK 12 early access build is now available here – http://jdk.java.net/12/ from Oracle for Linux, MacOS, and Windows. This early access, open source builds are under the [GNU General License, version 2 with Classpath Exception](https://openjdk.java.net/legal/gplv2+ce.html). This open-source build was created to generate feedback. However, not all functionalities will make it to the general availability release on March 19, 2019.

### Summary

It’s no secret that Java has made our online lives more convenient. It’s never been easier to write code in Java and develop programs that are secure and reliable.

Thank you to Full Scale for writing this guest post. If you’re in the market for hiring a Java developer, [Full Scale](https://fullscale.io/) offers Java Development services on full time or on a project basis. They specialize in augmenting your existing development team to scale up your team with additional remote developers. Be sure to check out their excellent blog post about [tips for managing remote development teams](https://fullscale.io/10-tips-for-managing-an-offshore-development-team/).

Also be sure to check out **Retrace**, Stackify’s APM solution to boosting application performance and quality at every stage of development. Some of the best features of Retrace are: app performance monitoring, code profiling, error tracking, centralized logging, and app & server metrics. [Get started today](https://stackify.com/retrace/).