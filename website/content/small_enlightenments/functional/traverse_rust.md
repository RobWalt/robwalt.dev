+++
title = "Traversable in Rust"
path = "small_enlightenments/functional/traverse_rust"
date = 2022-06-04
template = "page.html"
[taxonomies]
tags = ["functional programming", "rust", "traverse", "traversable"]
+++

A few days ago [my understanding of "Traversable"
improved](../traverse_haskell). Today I just want to reflect on that post and
take a look at how the example works in Rust.

The basic problem was, that we wanted to translate some DNA to RNA, but the
translation could fail at any point if we encountered an invalid base (a base
that's not G,C,A,T). So here is the basic setup with the heart-piece missing:

```rust
fn translate_base(base: char) -> Result<char, char> {
    match base {
        'G' => Ok('C'),
        'C' => Ok('G'),
        'A' => Ok('U'),
        'T' => Ok('A'),
        _ => Err(base),
    }
}

fn translate_dna(dna_strand: &str) -> Result<String, char> {
    // ???
    unimplemented!()
}

fn main() {
    let dna = String::from("GCTAGZZZGACT");
    let rna = translate_dna(&dna);
}
```

So how do we implement the missing piece? Well, even though the rust standard library doesn't actually give use something called "traverse" or "sequence", it's quiet simple:

```rust
fn translate_dna(dna_strand: &str) -> Result<String, char> {
    dna_strand
        .chars()
        .map(translate_base)
        .collect::<Result<String, char>>()
}
```

So "Traversable" was hidden in the standard library for the `std::result::Result` type all along 🤯. With a bit of naming we actually get (specifically for the `std::result::Result` type)

```rust
// .collect::<Result<T, E>>() =~= sequenceA

// .map(f).collect::<Result<T, E>>() == traverse
```

which is pretty neat.
