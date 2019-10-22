x# suslik-tutorial

The SuSLik web interface is [here](http://comcom.csail.mit.edu/comcom/#SuSLik)

## Exercises

1. [Rotate three](exercises/1-rotate.sus)
2. [Append two lists](exercises/2-list-append.sus)
2. [Schema migration](exercises/3-sll-to-dll.sus)

## SuSLik Cheatsheet

Here are some representative examples of different SuSLik constructs:

### Types

`bool`, `int`, `loc`, `set`

### Expressions

- Arithmetic: `x + 1`, `y - 2`, `5 * z`
- Sets: `{}`, `{1, 2} + {3}` (union), `{1, 2} * {2, 3}` (intersection), `s - {x}` (difference)
- Relational: `x == 1`, `x != 1`, `x <= 1`, `x < 1`, `x >= 1`, `x > 1`
    - with sets: `x in {1, 2}`, `{1} <= {1, 2}`
- Logical: `true`, `false`, `not x`, `x && y`, `x || y`, `x ==> y`

### Spatial Formulas

- Points-to: `x :-> 5`
- Block: `[x, 2]`
- Predicate instance: `list(x, {1, 2, 3})`
- Separating conjunction: `x :-> 5 ** (x + 1) :-> 7`

### Assertions

`{a > b ; x :-> a ** y :-> b}`

### Predicate Definitions

```
predicate list(loc x, set s) {
|  x == 0  => { s == {} ; emp }
|  x != 0  => { s == {v} + s1 ; [x, 2] ** x :-> v ** (x + 1) :-> nxt ** list(nxt, s1) }
}
```
