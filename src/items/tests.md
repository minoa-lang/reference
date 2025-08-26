# Tests)
```
<test-item>      := `test` <test-tags> <string-literal> [ <test-args> ] [ <test-condition> ] <block>
<test-tags>      := '[' <name> { ',' <name> } ']'
<test-args>      := '(' <fn-params> ')' 'with' {  <expr> ';' }*
<test-condition> := 'when' <expr>
```

A test item can be used to ensure that the program meets the expected behavior.
Test items are only compiled when the project is compiled as a test, and will not be present in code otherwise.

Test items are named, which is the name shown whenever the test is run.
In addition, a set of tags can also be provided, which is used to group different test and only run those tests which have the tag assigned to them.

Test can also be supplied with a list of arguments that the test can be run with.
The test first defines the arguments it takes, and is then followed by a list of argument sets.
An argument set, is a comma separated list of values.
Multiple argument sets can be separated by a `;`.

In addtion, a condition can be provided for when a test is availabe, e.g. only run tests on a given OS or when a given feature is enabled.

Whithin a test, either asserts can be used, or a result can be returned.
Test have an implicit `!impl Display` return type.

Test are run using a special test assert context, allowing them to capture each assert and report it.

By default, the language supplied test harness will be used, but this can be changed for a custom test harness.

> _Note_: In addition, by using the test-supplied allocator, leaks can be reported.
