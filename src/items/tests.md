# Tests
```
<test-item>      := 'test' <test-name> <test-tags> [ '(' <fn-params> ')' ] [ <test condition> ] <block> [ <test-args> ]
<test-name>      := <ext-name> | <string-literal>
<test-tags>      := '[' <test-name> { ',' <test-name> }* [ ',' ] ']'
<test-args>      := 'with` <block>
                 := 'with' <expr> ';'
<test-condition> := 'when' <expr>
```

A test item can be used to unit test a given piece of code, to ensure it follows expected behavior.
Test items are only compiled when the project is compiled as a test, and will otherwise not be present within code, including in any generated binaries or libraries.

Each test item is provided with a name, which can be an extended name or a string literal, this is than used to display the test's results, and can be used to skip the test.

Additionally, a test can be provided with a set of tags, which can be used to categorize different test, depending on some propertie they have.
These tags can be used to decide which tests to skip.

Tests are a special kind of function, and may therefore be provided with an explicit set of parameters.
This allows the user to write a generic test that can be used to check any set of possible input and expected values.
The arguments to this can be provided after the test, in one of the following ways:
- as a block yielding values to check against
- as an expression resulting in an array of tuples containing the arguments

Tests may also be prevented to run unless a condition is fulfilled, this can be done using a `when` clause with the condition.
For example, a test could only be run when on a specific OS.

> _Example_
> ```
> // Basic test
> test "1 + 1 == 2" {
>     assert(1 + 1 == 2);
> }
> 
> // Test with tags
> test "tag test" [ tag_a, tag_b ] {
>     // ...
> }
> 
> // test with condition
> test conditional when #target_os == .windows {
>     // ...
> }
> 
> // test with arguments
> test args (arg: String) {
>     println()
> } with [
>     "arg A",
>     "arg B",
> ];
> ```

Within the test, [asserts] are used to determine if the expected behavior is fulfilled, in this way, it is similar to [contracts].

> _Example_
> ```
> // Test will call both asserts, as it can determine they are independent from each other
> test "multiple asserts" {
>     a := 1;
>     b := 1;
>     c := 1;
> 
>     assert(a == 0);
> 
>     // does not rely on the result of the previous assert, so it can run both asserts.
>     assert(b == 1);
> 
>     // does rely on another assert, so will not be run when an assert using `b` fails
>     assert(c == 2, depend_on: b);
> }
> ```

Additionally, a result can also be returned from the test.
The test has an implicit `Result[(), impl Display]` return type.

> _Example_
> ```
> fn returns_err() -> DisplayableErr!() {
>     throw DisplayableErr();
> }
> 
> test "returns value" {
>     return_err()
> }
> ```

A special error is avialable within a test which signals the standard test harness to skip a test programatically, this error is `SkipTestErr`.
This allows for a `when` like condition, but with a runtime calculated value, for example when something should only be partially tested based on a result generated within the test, or based on the test's arguments.

> _Example_
> ```
> test "this will be skiped programatically" {
>     if should_skip() {
>         throw SkipTestErr;
>     }
> 
>     // Regular code here
> }
> ```

By default, all tests will use the test harness provided within the standard library, but this can be replaced using a custom test harness.

All tests are provided with the following:
- an assert context to capture all asserts
- a test allocator, allowing memory leaks to be reported
- a logger to output any additional info
- a random seed, e.g. to initialize an RNG

These can be accessed via the implicit context.

The test will try to run multiple asserts whenever the compiler can determine that they are independent from the success of other asserts.

Test must appear directly within a module.



[contracts]: ../constracts.md
[asserts]:   ../constracts.md#asserts-