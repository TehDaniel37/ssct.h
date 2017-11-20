# ssct.h
A stupid simple [XUnit](https://en.wikipedia.org/wiki/XUnit) type test "framework" that makes writing unit tests in C stupidly simple.

### Writing tests
1. Download ssct.h and place it in your project include directory
2. `#include "ssct.h"` in your test files
3. Write your test functions and use `ssct_assert_true`, `ssct_assert_equals`, etc.
4. Pass the test functions to `ssct_run` to run them
5. Call `ssct_print_summary()` to output ran/succeeded/failed test functions

### Example test file
```C
1   #include "ssct.h"
2   #include "my_implementation.h"
3   
4   static void foo_should_return_bar() {
5       const char *expected = "bar";
6       char *actual = foo();
7
8       ssct_assert_equals(actual, strlen(actual), expected, strlen(expected));
9   }
10
11  int main(void) {
12      ssct_run(foo_should_return_bar);
13
14      ssct_print_summary();
15
16      return 0;
17  }
```

Running the test file outputs:
```
Assertion failed for foo_should_return_bar() on line 8. Was foo but expected bar.
Test test.c: Ran 1 test. 0 succeeded and 1 failed.
```

### Macros
#### Assertions
- ssct_assert_true(test)
- ssct_assert_false(test)
- ssct_assert_equals(int1, int2)
- ssct_assert_equals(float1, float2, delta)
- ssct_assert_equals(str1, str1_len, str2, str2_len)
- ssct_assert_zero(int)
- ssct_assert_empty(str1)
- ssct_fail()

#### Other
- ssct_run(function) : *Run a test function*
- ssct_print_summary() : *Print tests ran/successful/failed*

### Setup & Teardown
ssct supports setup and teardown functions that are called before/after each of the test functions via function pointers. To use setup/teardown simply set the `ssct_setup` and `ssct_teardown` variables defined in the header file to point to your setup and teardown functions. 

#### Example using setup & teardown
```C
1   #include "ssct.h"
2   #include "my_implementation.h"
3   
4   struct FooBar *foo_bar;
5   
6   static void setup() {
7       puts("Allocating foo_bar");
8       foo_bar = (struct FooBar *)malloc(sizeof(struct FooBar));
9   }
10  
11  static void teardown() {
12      puts("Freeing foo_bar");
13      free(foo_bar);
14  }
15  
16  static void foo_bar_init_should_set_foo_to_5() {
17      foo_bar_init(&foo_bar);
18      assert_equals(foo_bar.foo, 5);
19  }
20  
21  int main(void) {
22      ssct_setup = setup;
23      ssct_teardown = teardown;
24      
25      ssct_run(foo_bar_init_should_set_foo_to_5);
26      
27      ssct_print_summary();
28      
29      return 0;
30  }
```

Running this test file outputs:
```
Allocating foo_bar
Assertion failed for foo_bar_init_should_set_foo_to_5() on line 18. Was 0 but expected 5.
Freeing foo_bar
Test test.c: Ran 1 test. 0 succeeded and 1 failed.
```
