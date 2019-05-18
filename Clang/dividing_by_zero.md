# Dividing By Zero

Integer division and floating point divison are handled differently by the compiler.

## Integer division

Integer divison will result in an exception being thrown.

```
int x = 0;
int y = 0;
NSLog(@"%d", x / y);  // EXC_ARITHMETIC (code=EXC_I386_DIV, subcode=0x0)
```

## Floating point division

Clang implement floating point division per the
[IEEE 754](https://en.wikipedia.org/wiki/IEEE_754#Exception_handling)
([spec](https://people.eecs.berkeley.edu/~wkahan/ieee754status/IEEE754.PDF)),
which defines valid return values for different types of division by zero.
However it will not throw an exception.

The summary here is that `NaN` will be returned for indeterminate results
(`0.0 / 0.0`) while `±infinity` will be returned for determinate results (`x / 0.0`,
where `x != 0`).

```
float a = 0.0f;
float b = 0.0f;
NSLog(@"%f", a / b);  // Prints not-a-number `nan`
```

```
float a = 5.0f;
float b = 0.0f;
NSLog(@"%f", a / b);  // Prints infinity `inf`
```

```
float a = -5.0f;
float b = 0.0f;
NSLog(@"%f", a / b);  // Prints negative infinity `-inf`
```

### Detecting floating point divison by zero

The simplest way to detect floating point division at runtime is to use the
[Undefined Behavior Sanitizer](https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html).

If working in Xcode, it can be enabled by following the instructions
[here](https://developer.apple.com/documentation/code_diagnostics/undefined_behavior_sanitizer/enabling_the_undefined_behavior_sanitizer?language=objc).
