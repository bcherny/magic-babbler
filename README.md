# magic-babbler

## How it works

Given a type signature:

```ts
// A[] -> A[]
array.of('A').to(array).of('A')
```

And a library of built-in unary functions:

```ts
function increment(n: number) {
  return n + 1
}
function map<A, B>(f: (a: A, i: number, as: A[]) => B) {
  return (as: A[]): B[] =>
    a.map(f)
}
function identity<A>(a: A): A {
  return a
}
...
```

We can mechanically generate a function that maps from the input type in the type signature to the final output type (traversing through any intermediate types along the way of course):

```ts
function x<A>(as: A[]): A[] {
  return identity(as)
}
function y<A>(as: A[]): A[] {
  return as.map(indentity)
}
function x<A>(as: A[]): A[] {
  return identity(identity(as))
}
...
```

Score each point by simplicity (rough metric for 1st pass: "how many nodes does this answer's AST contain?").

We use babel-types and starting with `File` and `Program` nodes (whose `body` contains exactly one `statement` each), we construct grammatically valid programs by recursivley generating children for each node (inspecing each node definition's `fields` so we know what types of children we can correctly generate).

This is just a BFS over the graph of syntactically valid programs starting at one node, and terminating at a second node.

Past some point of complexity, we may not be able to generate valid programs from type alone; at that point the program prompts the user for a few unit test cases.

```ts

```

TODO: Inspect some real programs and see how many of them decompose into

## Approach

In general, if we want to generate a program given some constraints, there are a few approaches we could take. From simplest (and least efficient) to more complex (and more efficient):

1. Generate random characters and keep evaluating a string until it produces a valid program that satisfies some unit tests.
2. Repeat with random AST nodes instead of random characters.
3. Repeat with random AST nodes, only a subset of which is available at each step (just grammatically valid ones). Babel-types has per-node children types neatly described with TypeScript already.