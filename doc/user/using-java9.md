# Using TruffleRuby with JDK 9

TruffleRuby is designed to be run with a JVM that has the Graal compiler. The
easiest way to do this is to use the GraalVM, which includes JDK 8, the Graal
compiler, and TruffleRuby, all in one package.

It is also possible to run on Java 9, but we do not support this and at the
moment we only document it for interest.

To run on Java 9, Graal needs to be built with Java 9 and there is no binary
distribution of GraalVM based on Java 9 so you will need to
[build Graal yourself](../contributor/building-graal.md), using Java 9 as the
JDK. You should also build TruffleRuby with the same JDK.

```
$ GRAAL_REPO=...path to graal repository built with JDK 9...
$ export JAVACMD=...java 9...
$ bin/truffleruby \
    -J-XX:+UnlockExperimentalVMOptions \
    -J-XX:+EnableJVMCI \
    -J--module-path=$GRAAL_REPO/sdk/mxbuild/modules/org.graalvm.graal_sdk.jar:$GRAAL_REPO/truffle/mxbuild/modules/com.oracle.truffle.truffle_api.jar \
    -J--upgrade-module-path=$GRAAL_REPO/compiler/mxbuild/modules/jdk.internal.vm.compiler.jar \
    -e 'p Truffle.graal?'
```

This should print `true` to say that compilation with Graal is enabled.

As another check you can run a loop and see that it is compiled.

```
$ bin/truffleruby \
    -J-XX:+UnlockExperimentalVMOptions \
    -J-XX:+EnableJVMCI \
    -J--module-path=$GRAAL_REPO/sdk/mxbuild/modules/org.graalvm.graal_sdk.jar:$GRAAL_REPO/truffle/mxbuild/modules/com.oracle.truffle.truffle_api.jar \
    -J--upgrade-module-path=$GRAAL_REPO/compiler/mxbuild/modules/jdk.internal.vm.compiler.jar \
    -J-Dgraal.TraceTruffleCompilation=true \
    -e 'loop { 14 + 2}'
[truffle] opt done ......
```

You may see this warning caused by `jnr-posix`, but it can be safely ignored
for now.

```
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by jnr.posix.JavaLibCHelper (file:.../truffleruby.jar) to method sun.nio.ch.SelChImpl.getFD()
WARNING: Please consider reporting this to the maintainers of jnr.posix.JavaLibCHelper
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
```
