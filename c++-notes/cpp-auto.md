# Cpp Note7: auto

_update Aug 4, 2019_

`auto` 使用和templates相同的type deduction mechanism, 不过对于brace-init lists, `auto` 会将其 deduce 为 `std::initializer_list`.

### `auto x = expression;`

去掉reference和cv qualifiers，然后match type。例如对于 `const int& f(){...}`，那么 `auto x = f()` 会 deduce `x` 为 `int`，而不是 `const int&`;

### `auto& x = expression;` and `const auto&`

保留cv qualifiers，因此对于之前那个`f()`， `auto& x = f()` 会 deduce 为 `const int&`， 而 `const auto&` 只是在这个基础上强制添加const；

### `auto&& x = expression;`

使用 reference-collapsing rules, 和template code 中的 forwarding references 类似。如果x是lvalue，则deduce lvalue reference, 否则deduce rvalue reference.

## 总结

使用 `auto` 如果需要一个 copy，否则用 `auto&` 或者 `const auto&` 依情况而定。

