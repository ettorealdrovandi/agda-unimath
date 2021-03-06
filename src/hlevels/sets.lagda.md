---
title: "h-Levels: Sets"
description: "Examples of known types that are  propositions or sets"
---

### Contents {#top}

1. [The empty type](#empty)
1. [The unit type](#unit)
1. [Sum types](#sumtypes)
1. [Ξ£-types](#sigma-types)
   1. [Cartesian products](#cartesian)

--------------------------------------------------

```agda
{-# OPTIONS --without-K --safe --exact-split #-}

module hlevels.sets where

open import level
open import mltt
open import function.core
open import function.homotopyequivalence
open import hlevels.core
open import homotopy.twocatconstructions.core
open import homotopy.twocatconstructions.ap2
open import homotopy.fibrations


open β‘-Reasoning
open βΎ-lemmas
open transport-lemmas
```

### The empty type {#empty}

The empty type is a proposition (not completely trivial) and hence a set.

```agda
π-is-prop : is-prop π
π-is-prop = Ξ» x y β !π y

π-is-set : is-set π
π-is-set = propβset π-is-prop
```
We can give a direct proof of the latter:

```agda
π-is-set' : is-set π
π-is-set' = Ξ» x y p q β !π y
```

### The unit type {#unit}

```agda
π-is-contr : is-contr π
π-is-contr = * , (π-induction (_β‘_ *) refl)

π-is-prop : is-prop π
π-is-prop = singletonβprop π-is-contr

π-is-set : is-set π
π-is-set = propβset π-is-prop
```

Contractible types are isomorphic to `π`, so we can prove the
"trivial" identity principle for `π` from the HoTT book

```agda
iscontr-iso-π : β {β} {A : Set β} β is-contr A β A β π
iscontr-iso-π is = hoeq (Ξ» _ β *)
                        (Ξ» _ β center is)
                        (Ξ» { * β refl})
                        (centrality is)

Idπ-is-π : {x y : π} β (x β‘ y) β π
Idπ-is-π {x} {y} = iscontr-iso-π (is x y)
  where
    is : π isofhlevel 1
    is = propβhlevel1 π-is-prop


Idπ-is-π' : {x y : π} β (x β‘ y) β π
Idπ-is-π' = record { to = Ξ» _ β *
                   ; from = Ξ» _ β refl
                   ; Ξ΅ = Ξ» { * β refl}
                   ; Ξ· = Ξ» { refl β refl}}
```

### Sum types {#sumtypes}

If `A` and `B` are sets, then so is their sum  `A β B`:

```agda
sum-isset : β {β β'} {A : Set β} {B : Set β'} β isSet A β isSet B β isSet (A β B)
sum-isset issetA issetB (inl a)  (inl a') p q = p β‘β¨ ap-inl-linv p β»ΒΉ β©
                                                ap inl p' β‘β¨ ap2 inl Ξ± β©
                                                ap inl q' β‘β¨ ap-inl-linv q β©
                                                q β
  where
    -- any path between the injected elements in A β B comes from a
    -- path in A between the elements themselves
    inl-lc : β {β β'} {A : Set β} {B : Set β'} {a a' : A}
             (p : _β‘_ {A = A β B} (inl a) (inl a')) β a β‘ a'
    inl-lc refl = refl

    ap-inl-linv : β {β β'} {A : Set β} {B : Set β'} {a a' : A}
                  (p : _β‘_ {A = A β B} (inl a) (inl a')) β
                  ap inl (inl-lc p) β‘ p
    ap-inl-linv refl = refl

    p' q' : a β‘ a'
    p' = inl-lc p
    q' = inl-lc q

    f = issetA

    Ξ± : p' β‘ q'
    Ξ± = f a  a' p' q'

sum-isset issetA issetB (inr b) (inr b') p q = p β‘β¨ ap-inr-linv p β»ΒΉ β©
                                               ap inr p' β‘β¨ ap2 inr Ξ± β©
                                               ap inr q' β‘β¨ ap-inr-linv q β©
                                               q β
  where
    -- do the same for the right injection inr : B β A β B
    inr-lc : β {β β'} {A : Set β} {B : Set β'} {b b' : B}
             (q : _β‘_ {A = A β B} (inr b) (inr b')) β b β‘ b'
    inr-lc refl = refl

    ap-inr-linv : β {β β'} {A : Set β} {B : Set β'} {b b' : B}
                  (q : _β‘_ {A = A β B} (inr b) (inr b')) β
                  ap inr (inr-lc q) β‘ q
    ap-inr-linv refl = refl

    p' q' : b β‘ b'
    p' = inr-lc p
    q' = inr-lc q

    g = issetB

    Ξ± : p' β‘ q'
    Ξ± = g b  b' p'  q'
```


### Ξ£-types {#sigma-types}

```agda
Ξ£-type-isset : β {β β'} {A : Set β} {P : A β Set β'} β 
               isSet A β ((x : A) β isSet (P x)) β isSet (Ξ£ A P)
Ξ£-type-isset {P = P} issetA issetP (x , s) (y , t) p  q = p  β‘β¨ Ξ³ β©
                                                                 q' β‘β¨ Ξ΄ β©
                                                                 q β
             where
               pp qq : (x , s) β (y , t)
               pp = PathΞ£βPathPair p
               qq = PathΞ£βPathPair q

               pβ qβ : x β‘ y
               pβ = Οβ pp
               qβ = Οβ qq

               -- the paths in the base are equal
               Ξ± : pβ β‘ qβ
               Ξ± = issetA x y pβ qβ

               -- in the fiber over y
               pβ : transport P pβ s β‘ t
               pβ = Οβ pp

               qβ : transport P qβ s β‘ t
               qβ = Οβ qq

               -- After breaking the paths with `PathPair`, we get different
               -- transports in the fiber over the endpoint of the path in the base.

               r : transport P pβ s β‘ transport P qβ s
               r = transportβ‘ Ξ±

               Ξ² : pβ β‘ r βΎ qβ
               Ξ² = issetP y (transport P pβ s) t pβ  (r βΎ qβ)

               qq' : (x , s) β (y , t)
               qq' = pβ , (r βΎ qβ)

               Ξ³Ξ³ : pp β‘ qq'
               Ξ³Ξ³ = pp β‘β¨ refl β©
                    (pβ , pβ) β‘β¨ ap (Ξ» v β pβ , v) Ξ² β©
                    (pβ , (r βΎ qβ)) β‘β¨ refl β©
                    qq' β

               q' : (x , s) β‘ (y , t)
               q' = PathPairβPathΞ£ qq'

               Ξ³ : p β‘ q'
               Ξ³ = p β‘β¨ (PathΞ£-equiv {q = p}) β»ΒΉ β©
                   PathPairβPathΞ£ pp β‘β¨ ap PathPairβPathΞ£ Ξ³Ξ³ β©
                   PathPairβPathΞ£ qq' β‘β¨ refl β©
                   q' β

               -- Move the path in the base and, accordingly, shift the corresponding
               -- path in the fiber:

               lemma : β {β β'} {A : Set β} {P : A β Set β'} {u v : Ξ£ A P}
                       ((p , q) : u β v) {p' : Οβ u β‘ Οβ v} (Ξ± : p β‘ p') β
                       (p , q) β‘ (p' , ((transportβ‘ Ξ±) β»ΒΉ βΎ q))
               lemma (p , q) {.p} refl = refl

               Ξ΄Ξ΄ : qq' β‘ qq
               Ξ΄Ξ΄ = qq' β‘β¨ lemma qq' Ξ± β©
                    qβ , (r β»ΒΉ βΎ (r βΎ qβ)) β‘β¨ ap (Ξ» v β (qβ , v)) (assoc (r β»ΒΉ) r qβ) β»ΒΉ β©
                    qβ , ((r β»ΒΉ βΎ r) βΎ qβ) β‘β¨ ap (Ξ» v β (qβ , v)) ((linv r) βΎΚ³ qβ) β©
                    qβ , (refl βΎ qβ) β‘β¨ refl β©
                    qq β

               Ξ΄ : q' β‘ q
               Ξ΄ = q' β‘β¨ refl β©
                   PathPairβPathΞ£ qq' β‘β¨ ap PathPairβPathΞ£ Ξ΄Ξ΄ β©
                   PathPairβPathΞ£ qq β‘β¨ PathΞ£-equiv {q = q} β©
                   q β

Ξ£-type-is-set = Ξ£-type-isset
```

Simpler proof for the corresponding fact for propositions
```agda
Ξ£-type-isprop : β {β β'} {A : Set β} {P : A β Set β'} β 
                isProp A β ((x : A) β isProp (P x)) β isProp (Ξ£ A P)
Ξ£-type-isprop {P = P} ispa i u v = PathPairβPathΞ£ pp
  where
    pp : u β v
    Οβ pp = (ispa (Οβ u) (Οβ v))
    Οβ pp = i (Οβ v) (transport P p (Οβ u)) (Οβ v)
      where
        p : Οβ u β‘ Οβ v
        p = ispa (Οβ u) (Οβ v)

Ξ£-type-is-prop = Ξ£-type-isprop
```

#### Cartesian products {#cartesian}

the Cartesian products are a special case:

```agda
Γ-isset : β {β β'} {A : Set β} {B : Set β'} β
           is-set A β is-set B β is-set (A Γ B)
Γ-isset ia ib = Ξ£-type-isset ia (Ξ» _ β ib)

Γ-is-set = Γ-isset


Γ-isprop : β {β β'} {A : Set β} {B : Set β'} β
           isProp A β isProp B β isProp (A Γ B)
Γ-isprop ia ib = Ξ£-type-isprop ia (Ξ» _ β ib)

Γ-is-prop = Γ-isprop
```

<p style="font-size: smaller; text-align: right">[top β](#top)</p>
---
