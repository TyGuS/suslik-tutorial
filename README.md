The SuSLik web interface is [here](https://suslik.programming.systems/)

## Specifications Cheatsheet

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

## Useful SSL rules

**Emp** Closes the derivation once the heaps are empty
```
            φ ⇒ ψ
------------------------------- [emp]
{φ ; emp} --> {ψ ; emp} | skip
```

**Frame** Removes identical heaplets from pre- and post-condition
```
     {φ ; P} --> {ψ ; Q} | c
------------------------------- [frame]
{φ ; P * R} --> {ψ ; Q * R} | c
```

**Read** Reads a ghost variable from the heap:
```
[y/a]{φ ; x -> a * P} --> [y/a]{ψ ; Q} | c
----------------------------------------------- [read]
{φ ; x -> a * P} --> {ψ ; Q} | let y := *x ; c
```

**Write** Writes a desired non-ghost exression into the heap:
```
{φ ; x -> e * P} --> {ψ ; x -> e * Q} | c
--------------------------------------------------- [write]
{φ ; x -> _ * P} --> {ψ ; x -> e * Q} | *x := e ; c
```

**Free** Frees a memory block we have in the pre
```
{φ ; P} --> {ψ ; x -> e * Q} | c
----------------------------------------------------------------------- [free]
{φ ; [x,n+1] * x -> _ * ... * (x,n) -> _ * P} --> {ψ ; Q} | free(x) ; c
```

**Alloc** Allocates a memory block that is needed in the post and starts at an existential
```
x is existential
{φ ; [x1,n+1] * x1 -> a1 * ... * (x1,n) -> an * P} --> {ψ ; [x1,n] * x1 -> e1 * ... * (x1,n) -> en * Q} | c
---------------------------------------------------------------------------------------------------------- [alloc]
{φ ; P} --> {ψ ; [x,n+1] * x -> e1 * ... * (x,n) -> en * Q} | x1 = malloc(n) ; c
```

**Open** Unfold predicate in precondition
```
{φ ; clause1(e) * P} --> {ψ ; Q} | c1   {φ ; clause2(e) * P} --> {ψ ; Q} | c1
------------------------------------------------------------------------------- [open]
             {φ ; pred(e) * P} --> {ψ ; Q} | if (guard) {c1} else c2
```

**Close** Unfold predicate in postcondition
```
{φ ; P} --> {ψ ; clause_i(e) * Q} | c
-------------------------------------- [close]
   {φ ; P} --> {ψ ; pred(e) * Q} | c
```

**Unify** Unify a heaplet with existentials in post with some heaplet in pre
```
x is existential
{φ ; P * [e/x]R} --> {ψ ; Q * [e/x]R} | c
------------------------------------------ [unify]
   {φ ; P * [e/x]R} --> {ψ ; Q * R} | c
```

**Pick** Replace an existential with any variable in scope of the same type
```
x is existential
{φ ; P} --> {ψ ; [e/x]Q} | c
----------------------------- [pick]
  {φ ; P} --> {ψ ; Q} | c
```
