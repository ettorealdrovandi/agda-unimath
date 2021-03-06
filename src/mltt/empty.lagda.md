---
title: "Empty type"
subtitle: "Martin-LΓΆf Type Theory"
description: "A minimal Type Theory in Martin-LΓΆf style: Empty type"
---

```agda
{-# OPTIONS --without-K --safe #-}

module mltt.empty where

open import level

data π : Set where  --complete definition, no constructor
```

#### Induction and recursion principles for π:

```agda
π-induction : β {β} (P : π β Set β) β (x : π) β P x
π-induction P ()

π-recursion : β {β} (B : Set β) β π β B
π-recursion B = π-induction (Ξ» _ β B) 

-- Direct definition
π-recursion' : β {β} (B : Set β) β π β B
π-recursion' B ()

-- Same but B implicit
π-recursion'' : β {β} {B : Set β} β π β B
π-recursion'' ()
```

#### Categorical properties

There is a unique function from π to any type

```agda
!π : β {β} {B : Set β} β π β B
!π = π-recursion''
```

#### Emptiness

The empty type can be used as a predicate to express emptinessβ¦
```agda
is-empty : β {β} β Set β β Set β
is-empty B = B β π
```
β¦or falsity:
    
```agda
-- logically
Β¬ : β {β} β Set β β Set β
Β¬ B = B β π
```
