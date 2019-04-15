# Testing support

Blade provides complete support for test-driven development, and can run test programs automatically through commands.

## incremental test
Blade test supports incremental testing to speed up the execution of tests.

Tests that have already been Passed do not need to run again in the next build and test unless:

* Any dependency changes to tests cause it to regenerate.
* tests dependent test data changes, this dependency is explicitly dependent, the user needs to specify using a BUILD file, such as testdata.
* tests where the environment variable has changed.
* test arguments change.
* Fail's test cases, rerun each time.

## Full test

If you need to use the full test, use the --full-test option, such as blade test common/... --full-test , all tests need to run.
In addition, cc_test supports the always_run attribute, which is used to always run every time during incremental testing, regardless of the last execution result.
```python
cc_test(
    name = 'zookeeper_test',
    srcs = 'zookeeper_test.cc',
    always_run = True
)
```

## Parallel testing

The Blade test supports parallel testing. The parallel test runs the test cases that need to be run after this build.
blade test [targets] --test-jobs N
-t, --test-jobs N Set the number of concurrent tests for concurrent tests. Blade will let N test processes execute in parallel.

## Non-parallel testing
For some tests that may not run in parallel because they may interfere with each other, you can add the exclusive attribute.
```python
cc_test(
    name = 'zookeeper_test',
    srcs = 'zookeeper_test.cc',
    exclusive = True
)
```


## Test coverage
When building and running tests, with the --coverage parameter, the blade will include coverage-related compile options, currently only C++ and Java.

C/C++ test coverage is achieved by gcc's [gcov](https://gcc.gnu.org/onlinedocs/gcc/Gcov.html). After the test is run, you need to execute a third-party tool such as gcov or lcov to generate a test coverage report.

Java test coverage is generated by the jacoco library, please read [instructions for use](../../java/README.md).


## Skip the specified test
Blade supports explicitly skipping specified tests with the --skip-tests parameter, which is useful when you need to run a large number of tests in batches and expect to skip some tests. E.g:
```bash
blade test base/... --skip-tests=base/string,base/encoding:hex_test
```
Indicates to run all tests in the base directory, but skip all tests under base/string and base/encoding:hex_test.