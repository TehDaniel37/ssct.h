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
#include "ssct.h"
#include "my_implementation.h"

static void foo_should_return_bar() {
    const char *expected = "bar";
    char *actual = foo();
    
    ssct_assert_equals(actual, strlen(actual), expected, strlen(expected));
}

int main(void) {
    ssct_run(foo_should_return_bar);
    
    ssct_print_summary();
    
    return 0;
}
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
#include "ssct.h"
#include "my_implementation.h"

struct FooBar *foo_bar;

static void setup() {
    foo_bar = (struct FooBar *)malloc(sizeof(struct FooBar));
}

static void teardown() {
    free(foo_bar);
}

static void foo_bar_init_should_set_foo_to_5() {
    foo_bar_init(&foo_bar);
    assertEquals(foo_bar.foo, 5);
}

int main(void) {
    ssct_setup = setup;
    ssct_teardown = teardown;
    
    ssct_run(foo_bar_init_should_set_foo_to_5);
    
    ssct_print_summary();
    
    return 0;
}
```
