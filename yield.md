# Difference between `yield` and `return`

| Return                                                   | Yield                                                        |
| -------------------------------------------------------- | ------------------------------------------------------------ |
| Returns the result to the caller                         | Used to convert a *function* to a *generator*. Suspends the function preserving its state. |
| Destroys the variables once execution is complete        | Yield does not destroy the functions local variables. Preserves the state. |
| There is usually one return statement per function       | There can be one more yield statements, which is quite common |
| If you execute a function again it starts from beginning | The execution begins from where it was previously paused     |

