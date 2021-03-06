---
title: "Unit type"
subtitle: "Martin-LΓΆf Type Theory"
description: "A minimal Type Theory in Martin-LΓΆf style: Unit type"
---


```agda
{-# OPTIONS --without-K --safe #-}

module mltt.unit where

open import level
```

We could define this as a data type as
```agda-ignore
data π : Set where
  * : π
```

```agda
record π : Set where
  constructor *
```

#### Induction and recursion principles

```agda
π-induction : β {β} (P : π β Set β) β P * β (x : π) β P x
π-induction P a * = a

π-recursion : β {β} (B : Set β) β B β π β B
π-recursion B = π-induction (Ξ» _ β B)

-- direct version
π-recursion' : β {β} (B : Set β) β B β π β B
π-recursion' B b * = b

-- implicit version
π-rec : β {β} {B : Set β} β B β π β B
π-rec {β} {B} = π-recursion {β} B
```

#### Categorical property

There is a unique function from any type to π:

```agda
!π : β {β} {B : Set β} β B β π
!π b = *
```

