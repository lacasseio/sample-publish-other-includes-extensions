# Allow additional C++ headers extension

## Preparation

```
$ ./gradlew publishAll
```

## The problem

Project dependencies works:

```
$ ./gradlew assembleRelease

BUILD SUCCESSFUL
9 actionable tasks: 2 executed, 7 up-to-date
```

But not binary dependencies:

```
$ ./gradlew assembleRelease -PfromRepo

> Task :app:compileReleaseCpp FAILED
./app/src/main/cpp/app.cpp:8:10: fatal error: 'foo.i' file not found
#include "foo.i"
         ^~~~~~~
1 error generated.


FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':app:compileReleaseCpp'.
> A build operation failed.
      C++ compiler failed while compiling app.cpp.
  See the complete log at: file:///./app/build/tmp/compileReleaseCpp/output.txt
   > C++ compiler failed while compiling app.cpp.

* Try:
> Run with --stacktrace option to get the stack trace.
> Run with --info or --debug option to get more log output.
> Run with --scan to get full insights.

* Get more help at https://help.gradle.org

BUILD FAILED
5 actionable tasks: 2 executed, 3 up-to-date
```

Because Gradle core native plugin only consider [these file extensions](https://github.com/gradle/gradle/blob/3793d48c8b10f519dd59323fcf0efbfe8dc08d69/platforms/native/language-native/src/main/java/org/gradle/language/cpp/internal/DefaultCppLibrary.java#L151-L158) as valid headers when [building the header zip](https://github.com/gradle/gradle/blob/3793d48c8b10f519dd59323fcf0efbfe8dc08d69/platforms/native/language-native/src/main/java/org/gradle/language/cpp/plugins/CppLibraryPlugin.java#L165).

## The Solution

Configure the `cppHeaders` Zip task to include the additional headers of any `CppLibrary` components.

```
$ ./gradlew publishAll -PincludeFix

BUILD SUCCESSFUL
15 actionable tasks: 10 executed, 5 up-to-date
$ ./gradlew assembleRelease -PfromRepo

BUILD SUCCESSFUL
9 actionable tasks: 9 up-to-date
```