# Operators

The following table lists the operators that you can use:

| Category    | Description                                                  |
| :---------- | :----------------------------------------------------------- |
| Arithmetic  | +-*/ or "div"% or "mod"                                      |
| Logical     | and&&or\|\|not!                                              |
| Relational  | You can use the following operators to compare against other values, or against boolean, string, integer, or floating point literals.==eq!=ne<lt>gt<=ge>=le |
| Empty       | `empty`The empty operator is a prefix operation that can be used to determine whether a value is null or empty. |
| Conditional | ?For example, `A ? B : C` states that if A is true, then B. If A is not true, then C. |

## Operator Precedence

The precedence of operators highest to lowest, left to right is as follows:

- [ ]
- ( ) - Used to change the precedence of operators
- \- (unary) `not ! empty`
- \* / div % mod
- \+ - (binary)
- < > <= >= lt gt le ge
- == != eq ne
- && and
- || or
- ? :