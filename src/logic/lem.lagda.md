---
title: "Excluded Middle"
description: "Small facts about the excluded middle"
---

```agda
{-# OPTIONS --without-K --safe #-}

module logic.lem where

open import level
open import mltt
open import logic.negation
open import hlevels.core
```

There is a statement that *can* be proved in the context of Martin-LΓΆf Type Theory without assuming axioms: that the Excluded Middle is "irrefutable," namely that its double negation always holds.

In the following, recall that for any type, `Β¬ A = A β π`. In the proof we start with evidence `ΞΎ : Β¬ (A + Β¬ A)` that is, `ΞΎ : (A + Β¬ A) β π`.

```agda
EM-is-irrefutable : β {β} (A : Set β) β Β¬Β¬ (A β Β¬ A)
EM-is-irrefutable A ΞΎ =  ΞΎ  --takes a term of A + Β¬ A
                         (inr --we choose Β¬ A, since there's no A. But Β¬ A = A β π
                           Ξ» x β ΞΎ ( inl x)) --x : A so to produce π we apply ΞΎ to its left injection

```

The "wild" Law of the Excluded Middle simply asserts that we can peel those two negation operators off. This *cannot* be proved, but we can write its type:

```agda
wLEM : β β β Set (lsuc β)
wLEM β = (A : Set β) β A β Β¬ A
```

We state its converse, that is, peeling off one negation operator from triple negation, as an axiom and we can write its type:


```agda
wLDN : β β β Set (lsuc β)
wLDN β = (A : Set β) β (Β¬Β¬ A β A)
```

The two axioms *are* logically equivalent:

```agda
ldn-implies-lem : β {β} β wLDN β β wLEM β
ldn-implies-lem {β} ldn A = ldn (A β Β¬ A) dn-em
  where
    dn-em = EM-is-irrefutable A

lem-implies-ldn : β {β} β wLEM β β wLDN β
lem-implies-ldn lem A Β¬Β¬a with (lem A)
lem-implies-ldn lem A Β¬Β¬a | inl x = x
lem-implies-ldn lem A Β¬Β¬a | inr x = !π (Β¬Β¬a x)

lem-implies-ldn' : β {β} β wLEM β β wLDN β
lem-implies-ldn' lem A = help (lem A)
  where
    help : (A β Β¬ A) β (Β¬Β¬ A β A)
    help (inl a)  = Ξ» _ β a
    help (inr Β¬a) = Ξ» Β¬Β¬a β !π {B = A} (Β¬Β¬a Β¬a)
```

<span style="color: teal; font-weight: bold">Remark</span> It is important to note the form of the two lemmas for the logical equivalence of LEM and LDN. For example, LEM β LDN is *not* formulated as

    (A : Set β) β ( (A + Β¬ A) β (Β¬Β¬ A β A))

as one might expect, but as:

     ((A : Set β) β  (A + Β¬ A)) β (B : Set β) β (Β¬Β¬ B β B)

The difference is crucial: in order to prove LDN for a *given type $B$*, we need to know that LEM holds *for all* types.

A more reasonable version of the axiom is the following:

```agda
LEM : β β β Set (lsuc β)
LEM β = (A : Set β ) β is-prop A β (A β Β¬ A)
```
and

```agda
LDN : β β β Set (lsuc β)
LDN β = (A : Set β) β is-prop A β (Β¬Β¬ A β A)
```

For mere propositions there is a compelling alternative characterization worked out in [MHE][MHE].

```agda
LEM' : β β β Set (lsuc β)
LEM' β = (A : Set β) β is-prop A β (is-singleton A β is-empty A)
```

This says that if a type is a (mere) proposition, then it either is contractible or it is empty. The two versions are logically equivalent:

```agda
LEM-implies-LEM' : β {β} β LEM β β LEM' β
LEM-implies-LEM' lem A is with (lem A is)
LEM-implies-LEM' lem A is | inl  a = inl (inhabited-propβsingleton a is)
LEM-implies-LEM' lem A is | inr Β¬a = inr Β¬a

LEM'-implies-LEM : β {β} β LEM' β β LEM β
LEM'-implies-LEM lem' A is with (lem' A is)
... | inl issingl = inl (center {A = A} issingl)
... | inr Β¬a      = inr Β¬a
```

According to `LEM'`, a mere Proposition is either a singleton or it is empty. Without assuming it we can stil prove that *it is not the case* that there are mere proposition that are not singletons and also not empty. These are aptly called "unicorns" in [MHE][MHE]:

```agda
there-are-no-unicorns : β {β} β  Β¬ ( Ξ£[ A β Set β ]  ((isProp A) Γ ( (Β¬ (isSingleton A)) Γ (Β¬ (is-empty A)))  ) )
there-are-no-unicorns (A , isp , f , g) = nothing
  where
    Β¬a : is-empty A
    Β¬a = Ξ» x β f (inhabited-propβsingleton x isp)

    nothing : π
    nothing = g Β¬a
```

We have actually *proved* the above statement, so we can exclude these "unicorns" exist. Therefore, if LEM is not assumed, it is the case that the possible propositions are the contractible types and the empty one, however there is no way in our type theory to determine which of the two alternatives occurs for a given (mere) proposition `A`.



<p style="font-size: smaller; text-align: right">[top β](#top)</p>
---

