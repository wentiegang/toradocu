# Toradocu: automated generation of test oracles from Javadoc documentation

[![Build Status](https://travis-ci.org/albertogoffi/toradocu.svg?branch=master)](https://travis-ci.org/albertogoffi/toradocu)

Toradocu generates test oracles from the Javadoc documentation of a
class. Toradocu is described in the paper
[*Automatic Generation of Oracles for Exceptional Behaviors*](http://star.inf.usi.ch/star/papers/16-issta-toradocu.pdf)
by Alberto Goffi, Alessandra Gorla, Michael D. Ernst, and Mauro Pezzè (presented
at [ISSTA 2016](https://issta2016.cispa.saarland)).

Toradocu takes the source code of a class as input and produces a set of
[aspects](https://eclipse.org/aspectj/).


## Building Toradocu
To compile Toradocu run the command: `./gradlew shadowJar`
This will create the file `build/libs/toradocu-1.0-all.jar`.
Notice that building Toradocu requires Java JDK 1.8+. 


## Running Toradocu
Toradocu is a command-line tool.
A typical Toradocu invocation looks like this:

    java -jar toradocu-1.0-all.jar \
       --target-class mypackage.MyClass \
	   --test-class mypackage.MyTest \
	   --source-dir project/src \
	   --class-dir project/bin \
       --output-dir aspects \
	   
With those options, Toradocu generates
[AspectJ aspects](https://eclipse.org/aspectj/) in the folder `aspects` in your
working directory. Aspects generated by Toradocu are standard AspectJ aspects
and can be used to instrument an existing test suite. Please refer to the
[AspectJ documentation](https://eclipse.org/aspectj/doc/released/devguide/ajc-ref.html)
for more information.

To get the list of all command-line parameters, execute

      java -jar toradocu-1.0-all.jar --help

Internally Toradocu executes the `javadoc` tool. Every option starting with `-J`
will be passed to the `javadoc` tool. For example, you can specify the directory
where to save the Javadoc documentation with `-J-d=javadoc_output`. You can
customize the behavior of the `javadoc` tool using all its options.


## Using Toradocu Aspects Toradocu generates
[AspectJ aspects](https://eclipse.org/aspectj/) in the directory specified with
the option `--aspects-output-dir` (`aspects` in your working dir by default). In
the aspects output directory Toradocu places the source code of the aspects and
an `aop.xml` that lists the generated aspects and that is used by the AspectJ
compiler.

To augment an existing test suite with Toradocu's oracles you have to:

1. Generate the aspects with Toradocu.
2. Compile the generated aspects.
3. Weave the existing test suites and the system under test.
4. Run the weaved test suite.

How to generate the aspects with Toradocu is described in the
[previous section](#running-toradocu).

To compile the aspects you can use `javac`. Just be sure that
[JUnit](http://junit.org/junit4/), the AspectJ weaver, and your system under
test are on the classpath.

To weave the existing test suite and the system under test, you can use the
[AspectJ compiler](https://eclipse.org/aspectj/doc/next/devguide/ajc-ref.html).
The weaving of the system under test is needed for aspects that check the
behavior of constructors.

Run weaved test suite as a normal test suite, just be sure to have AspectJ on
the classpath.
