# Exercice - Median of Two Sorted Arrays

**Niveau :** Hard
**Pattern :** Binary Search for Kth Element
**Retour au cours :** [Binary Search Pattern](../../courses/03-binary-search.md)

## 📝 Énoncé

Étant donnés deux tableaux triés `nums1` et `nums2` de tailles `m` et `n` respectivement, retournez **la médiane** des deux tableaux triés.

La complexité temporelle globale doit être `O(log (m+n))`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums1 = [1,3], nums2 = [2]
Output: 2.00000
Explication: tableau fusionné = [1,2,3] et la médiane est 2
```

**Exemple 2 :**
```
Input: nums1 = [1,2], nums2 = [3,4]
Output: 2.50000
Explication: tableau fusionné = [1,2,3,4] et la médiane est (2 + 3) / 2 = 2.5
```

**Exemple 3 :**
```
Input: nums1 = [0,0], nums2 = [0,0]
Output: 0.00000
```

**Exemple 4 :**
```
Input: nums1 = [], nums2 = [1]
Output: 1.00000
```

**Exemple 5 :**
```
Input: nums1 = [2], nums2 = []
Output: 2.00000
```

## 🎯 Contraintes

- `nums1.length == m`
- `nums2.length == n`
- `0 <= m <= 1000`
- `0 <= n <= 1000`
- `1 <= m + n <= 2000`
- `-10^6 <= nums1[i], nums2[i] <= 10^6`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Pour O(log(m+n)), vous devez utiliser binary search. L'idée est de partitionner les deux arrays pour que la moitié gauche contienne exactement (m+n+1)/2 éléments.

</details>

<details>
<summary>Indice 2</summary>

Si vous partitionnez nums1 à l'index i et nums2 à l'index j, vous voulez que tous les éléments à gauche soient ≤ tous les éléments à droite.

</details>

<details>
<summary>Indice 3</summary>

Pour une partition valide : `maxLeft ≤ minRight`. Si ce n'est pas le cas, ajustez la partition avec binary search.

</details>

<details>
<summary>Indice 4</summary>

Faites binary search sur le plus petit array pour optimiser, et calculez la partition de l'autre array automatiquement.

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "math"
)

func findMedianSortedArrays(nums1 []int, nums2 []int) float64 {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Printf("%.5f\n", findMedianSortedArrays([]int{1, 3}, []int{2}))         // Expected: 2.00000
    fmt.Printf("%.5f\n", findMedianSortedArrays([]int{1, 2}, []int{3, 4}))      // Expected: 2.50000
    fmt.Printf("%.5f\n", findMedianSortedArrays([]int{0, 0}, []int{0, 0}))      // Expected: 0.00000
    fmt.Printf("%.5f\n", findMedianSortedArrays([]int{}, []int{1}))             // Expected: 1.00000
    fmt.Printf("%.5f\n", findMedianSortedArrays([]int{2}, []int{}))             // Expected: 2.00000
    fmt.Printf("%.5f\n", findMedianSortedArrays([]int{1, 2, 3}, []int{4, 5}))   // Expected: 3.00000
    fmt.Printf("%.5f\n", findMedianSortedArrays([]int{1, 3, 5}, []int{2, 4, 6})) // Expected: 3.50000
    fmt.Printf("%.5f\n", findMedianSortedArrays([]int{1}, []int{2, 3, 4}))      // Expected: 2.50000
    fmt.Printf("%.5f\n", findMedianSortedArrays([]int{3}, []int{1, 2, 4}))      // Expected: 2.50000
    fmt.Printf("%.5f\n", findMedianSortedArrays([]int{1, 2}, []int{1, 2}))      // Expected: 1.50000
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
import "math"

func findMedianSortedArrays(nums1 []int, nums2 []int) float64 {
    // S'assurer que nums1 est le plus petit array
    if len(nums1) > len(nums2) {
        return findMedianSortedArrays(nums2, nums1)
    }

    m, n := len(nums1), len(nums2)
    left, right := 0, m

    for left <= right {
        // Partitions
        partitionX := left + (right-left)/2
        partitionY := (m+n+1)/2 - partitionX

        // Éléments aux frontières
        var maxLeftX, maxLeftY, minRightX, minRightY int

        // Gérer les cas limites
        if partitionX == 0 {
            maxLeftX = math.MinInt32
        } else {
            maxLeftX = nums1[partitionX-1]
        }

        if partitionY == 0 {
            maxLeftY = math.MinInt32
        } else {
            maxLeftY = nums2[partitionY-1]
        }

        if partitionX == m {
            minRightX = math.MaxInt32
        } else {
            minRightX = nums1[partitionX]
        }

        if partitionY == n {
            minRightY = math.MaxInt32
        } else {
            minRightY = nums2[partitionY]
        }

        // Vérifier si la partition est correcte
        if maxLeftX <= minRightY && maxLeftY <= minRightX {
            // Partition trouvée !
            if (m+n)%2 == 0 {
                // Nombre pair d'éléments
                return float64(max(maxLeftX, maxLeftY)+min(minRightX, minRightY)) / 2.0
            } else {
                // Nombre impair d'éléments
                return float64(max(maxLeftX, maxLeftY))
            }
        } else if maxLeftX > minRightY {
            // On a pris trop d'éléments de nums1, aller à gauche
            right = partitionX - 1
        } else {
            // On a pris trop peu d'éléments de nums1, aller à droite
            left = partitionX + 1
        }
    }

    return 0.0
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}
```

**Explication détaillée :**

1. **Concept de partition :**
   - Diviser chaque array en deux parties
   - Partie gauche : `(m+n+1)/2` éléments au total
   - Partie droite : le reste

2. **Invariant recherché :**
   - `maxLeft ≤ minRight` pour les deux arrays
   - Si vrai → partition correcte, calculer médiane

3. **Binary search sur partition :**
   - Chercher sur le plus petit array (optimisation)
   - `partitionX` : nombre d'éléments de nums1 dans la partie gauche
   - `partitionY = (m+n+1)/2 - partitionX`

4. **Calcul de la médiane :**
   - **Nombre pair :** `(max(maxLeft) + min(minRight)) / 2`
   - **Nombre impair :** `max(maxLeft)`

**Exemple pour `nums1=[1,3], nums2=[2]` :**

```
m=2, n=1, total=3 (impair)
Partition target: (3+1)/2 = 2 éléments à gauche

partitionX=1, partitionY=1
nums1: [1] | [3]    maxLeftX=1, minRightX=3
nums2: [2] | []     maxLeftY=2, minRightY=∞

Condition: maxLeftX=1 ≤ minRightY=∞ ✓ et maxLeftY=2 ≤ minRightX=3 ✓
→ Médiane = max(1,2) = 2
```

**Complexité :**
- Temps : O(log(min(m,n)))
- Espace : O(1)

**Points clés :**
- Partition équilibrée des deux arrays
- Binary search sur la taille de partition
- Gestion soigneuse des cas limites (partitions vides)

</details>

## 🚀 Exercices similaires

- [Kth Smallest Element in Sorted Matrix](../medium/kth-smallest.md) - Généralisation du concept
- [Split Array Largest Sum](split-array.md) - Binary search sur réponse
- [Find K Closest Elements](../medium/k-closest.md) - Recherche avec contraintes

## 🎓 Concepts clés

- **Partition équilibrée** : diviser pour maintenir l'ordre global
- **Binary search sur index** : chercher la position optimale de coupe
- **Médiane par partition** : calcul sans fusion complète
- **Optimisation par taille** : binary search sur le plus petit array