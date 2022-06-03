+++
title = "The day I finally understood \"Traversable\""
path = "small_enlightenments/functional/traverse"
date = 2022-06-02
template = "page.html"
[taxonomies]
tags = ["functional programming", "haskell", "rust", "traverse"]
+++

While reading through [my haskell programming book](https://haskellbook.com/) I
loved learning all these new concepts from functional programming which were so
elegant in haskell. However, some of the stuff described in the later chapters
was still inaccessible to me because of lacking experience with haskell syntax
in real world problems. One such thing was the `Traversable` typeclass. The
type signature looked pretty weird to me, I didn't get it at the time and just
skipped it in the hope that I would one day in the future maybe understand it
when finishing the rest of the book. Well, spoiler alert ... I didn't learn
something from skipping a part of a book. However, as of today I'm at least a
step closer to understanding `Traversable` and I can at least name 1 (one!)
usecase. Let's dive into the topic.

So recently I'm trying to solve a short coding excercise in haskell each day to
sweep the dust of the haskell parts of my brain. As my fate wanted it, today I
was solving the [task of RNA Transcription on
exercism](https://exercism.org/tracks/haskell/exercises/rna-transcription)
which has a really nice solution when using the methods of `Traversable`. Of
course, I didn't know that before solving the task and only got to know it from
looking at other peoples code. You can even take a look at [my crappy solution
here](https://exercism.org/tracks/haskell/exercises/rna-transcription/solutions/aviac).

The task is basically just "transcribe this strand of DNA to RNA" which can of
course fail if we get some corrupt input data like

```hs
inputData :: String 
inputData = "GTAZZZCGAC"
```

Since `Z` is not a transcribable DNA base, we want to return the first
erroneous base indicating that an error happened. This gives our RNA
Transcription Program an overall type signature of

```hs
transcribeRna :: String -> Either Char String
```

On of the first reflexes here is for us to factor out the transcription of
individual chars and then somehow life this behaviour over the whole DNA
strand. Doing this is not to difficult and we end up with something like this:

```hs
transcribeBase :: Char -> Either Char Char
transcribeBase base = case base of 
    'G' -> Right 'C'
    'C' -> Right 'G'
    'T' -> Right 'A'
    'A' -> Right 'U'
    _ -> Left base
```

Here `Right` translates to success and `Left` translates to failure. Ok, fine.
Now we have a perfect base transcribing function. Let's map it over the DNA
strand

```hs
transcribedStrand :: ???
transcribedStrand = map transcribeBase inputData
```

Now what is the type of the output of this? Let's look at the types:

```hs
inputData :: String 
transcribeBase :: Char -> Either Char Char

transcribedStrand :: [Either Char Char]
transcribedStrand = map transcribeBase inputData

transcribeRna :: String -> Either Char String -- this is what we want
```

Well that doesn't really look like what we want 😞!
...
hmmm
...
But wait, let's look closer: We have two layers of functors here
```hs
[Either Char Char]

F ( G ( Char ) )

where

F = [  ] 
G = Either Char
```
Let's take a look at the output type of the program:
```hs
Either Char String
```
...
hmmm
...
But wait!! Isn't `String` just the same as `[Char]`. So there are also two layers of functors
```hs
Either Char String == Either Char [Char]

G ( F ( Char ) )

where
F = [  ] 
G = Either Char
```
So we basically just need to swap the functors and we're done. I wonder if
there is a function in the prelude that can solve this 🤔. But there is! Its
`sequenceA`. So after applying `sequenceA` to our results from above, we end up
with the types aligned, so let's try it out

```hs
transcribedStrand :: Either Char String
transcribedStrand = sequenceA . map transcribeBase inputData

transcribeRna :: String -> Either Char String -- this is matches perfectly
```

It works!! But we can improve this by a little bit still. If we look in [the
definition of the Traversable
trait](https://en.m.wikibooks.org/wiki/Haskell/Traversable), we can find out
that there exists a handy function `traverse` with

```hs
travers = sequenceA . map
```

So we can even just write 

```hs
oldTranscribedStrand :: Either Char String
oldTranscribedStrand = sequenceA . map transcribeBase inputData

newTranscribedStrand :: Either Char String
newTranscribedStrand = traverse transcribeBase inputData
```

Wow 😎, that was awesome! Now let's just summarize what happened here from a high-level viewpoint:

We could make use of the `Traversable` trait in a situation where we wanted to
transform a "list of results" to a "result of list" which should come in quiet
handy in various real world applications. From now, whenever we notice that
kind of pattern, we can utilize the very cool functions from `Traversable`.
