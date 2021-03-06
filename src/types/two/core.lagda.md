---
title: "The type with two elements"
subtitle: "Standard and simple types in Martin-LΓΆf Type Theory"
description: "Disjoint union (sum) of two unit types: the type with two canonical terms"
---


```agda
{-# OPTIONS --without-K --safe #-}

module types.two.core where

open import level
open import mltt.sum
open import mltt.unit
```

```agda
π : Set
π = π β π
-----
pattern β = inl *
pattern β = inr *
```

#### Induction and recursion principles

```agda
-- induction and recursion: direct definition
π-induction : β {β} (P : π β Set β) β P β β P β β (i : π) β P i
π-induction P pβ pβ β = pβ
π-induction P pβ pβ β = pβ


π-induction' : β {β} (P : π β Set β) β P β β P β β (i : π) β P i
π-induction' P pβ pβ = β-induction P 
                           (π-induction (Ξ» x β P (inl x)) pβ) 
                           (π-induction (Ξ» x β P (inr x)) pβ) 


-- recursion
π-recursion : β {β} (P : Set β) β P β P β π β P
π-recursion P = π-induction (Ξ» _ β P)
```

#### Types dependent on π

```agda
π-to-dep' : β {β} (A B : Set β) β π β Set β
π-to-dep' A B β = A
π-to-dep' A B β = B

π-to-dep : β {β} (A B : Set β) β π β Set β
π-to-dep {β} A B = π-recursion (Set β) A B
infix 40 _β_
_β_ = π-to-dep
```
