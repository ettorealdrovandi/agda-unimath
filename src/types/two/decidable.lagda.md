---
title: "The type with two elements"
subtitle: "Standard and simple types in Martin-LΓΆf Type Theory"
description: "Disjoint union (sum) of two unit types: the type with two canonical terms: π is decidable"
---


```agda
{-# OPTIONS --without-K --safe #-}

module types.two.decidable where

open import level
open import mltt.sum
open import mltt.unit
open import mltt.empty
open import mltt.identity.core
open import types.two.core
open import logic.negation
open import logic.decidable
```

```agda
β-is-not-β : β β’ β
β-is-not-β = β’-inv (inl-is-not-inr {A = π} {B = π} {*} {*})
```

```agda
π-decidable-equality : decidable-equality π
π-decidable-equality β β = inl (idp β)
π-decidable-equality β β = inr (β’-inv β-is-not-β)
π-decidable-equality β β = inr (β-is-not-β)
π-decidable-equality β β = inl (idp β)
```
