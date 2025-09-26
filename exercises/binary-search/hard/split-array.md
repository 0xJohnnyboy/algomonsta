# Exercice - Split Array Largest Sum

**Niveau :** Hard
**Pattern :** Binary Search on Answer Space
**Retour au cours :** [Binary Search Pattern](../../courses/03-binary-search.md)

## 📝 Énoncé

Étant donné un tableau d'entiers non négatifs `nums` et un entier `k`, divisez `nums` en `k` sous-tableaux **contigus** non vides.

Le **score** d'une division est la somme maximale parmi tous les sous-tableaux.

Retournez le **score minimum** possible d'une division de `nums` en `k` sous-tableaux.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [7,2,5,10,8], k = 2
Output: 18
Explication: Il y a quatre façons de diviser nums en deux sous-tableaux.
La meilleure façon est de diviser en [7,2,5] et [10,8],
où la plus grande somme parmi les deux sous-tableaux n'est que 18.
```

**Exemple 2 :**
```
Input: nums = [1,2,3,4,5], k = 2
Output: 9
Explication: Il y a quatre façons de diviser nums en deux sous-tableaux.
La meilleure façon est de diviser en [1,2,3] et [4,5],
où la plus grande somme parmi les deux sous-tableaux n'est que 9.
```

**Exemple 3 :**
```
Input: nums = [1,4,4], k = 3
Output: 4
Explication: Chaque élément dans son propre sous-tableau : [1], [4], [4]
```

## 🎯 Contraintes

- `1 <= nums.length <= 1000`
- `0 <= nums[i] <= 10^6`
- `1 <= k <= min(50, nums.length)`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème de "Binary Search on Answer Space". L'espace de recherche est la valeur du score, pas un tableau.

</details>

<details>
<summary>Indice 2</summary>

Le score minimum possible est `max(nums)` (chaque élément dans son propre sous-tableau si k >= n).
Le score maximum possible est `sum(nums)` (tout dans un seul sous-tableau si k = 1).

</details>

<details>
<summary>Indice 3</summary>

Pour un score candidat, écrivez une fonction qui vérifie s'il est possible de diviser le tableau en k sous-tableaux avec ce score maximum.

</details>

<details>
<summary>Indice 4</summary>

Si on peut diviser avec un score S, peut-on diviser avec un score S+1 ? Cette propriété monotone justifie le binary search.

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "math"
)

func splitArray(nums []int, k int) int {
    // Votre code ici

}

// Fonction helper : peut-on diviser nums en au plus k sous-tableaux
// avec somme maximale <= maxSum ?
func canSplit(nums []int, k int, maxSum int) bool {
    // Votre code ici

}

// Fonction helper pour calculer la somme du tableau
func sum(nums []int) int {
    // Votre code ici

}

// Fonction helper pour trouver le maximum du tableau
func max(nums []int) int {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println(splitArray([]int{7, 2, 5, 10, 8}, 2))    // Expected: 18
    fmt.Println(splitArray([]int{1, 2, 3, 4, 5}, 2))     // Expected: 9
    fmt.Println(splitArray([]int{1, 4, 4}, 3))           // Expected: 4
    fmt.Println(splitArray([]int{1}, 1))                 // Expected: 1
    fmt.Println(splitArray([]int{1, 2, 3}, 1))           // Expected: 6
    fmt.Println(splitArray([]int{10, 5, 13, 4, 8, 4, 5, 11, 14, 9, 16, 10, 20, 8}, 8)) // Expected: 25
    fmt.Println(splitArray([]int{2, 3, 1, 2, 4, 3}, 5))  // Expected: 4
    fmt.Println(splitArray([]int{1, 2, 3, 4, 5, 6, 7}, 3)) // Expected: 9
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func splitArray(nums []int, k int) int {
    // Définir l'espace de recherche
    left := max(nums)    // Minimum possible : le plus grand élément
    right := sum(nums)   // Maximum possible : somme totale

    // Binary search sur la réponse
    for left < right {
        mid := left + (right-left)/2

        if canSplit(nums, k, mid) {
            // mid fonctionne, essayer plus petit
            right = mid
        } else {
            // mid trop petit, augmenter
            left = mid + 1
        }
    }

    return left
}

func canSplit(nums []int, k int, maxSum int) bool {
    subarrays := 1
    currentSum := 0

    for _, num := range nums {
        if currentSum+num > maxSum {
            // Commencer un nouveau sous-tableau
            subarrays++
            currentSum = num

            // Si on a besoin de plus de k sous-tableaux, impossible
            if subarrays > k {
                return false
            }
        } else {
            currentSum += num
        }
    }

    return true
}

func sum(nums []int) int {
    total := 0
    for _, num := range nums {
        total += num
    }
    return total
}

func max(nums []int) int {
    maxVal := nums[0]
    for _, num := range nums {
        if num > maxVal {
            maxVal = num
        }
    }
    return maxVal
}
```

**Explication détaillée :**

1. **Espace de recherche :**
   - **Minimum :** `max(nums)` - chaque élément doit être dans un sous-tableau
   - **Maximum :** `sum(nums)` - tout dans un seul sous-tableau

2. **Fonction de validation `canSplit` :**
   - Algorithme greedy : remplir chaque sous-tableau au maximum
   - Si la somme courante + prochain élément > maxSum → nouveau sous-tableau
   - Si on dépasse k sous-tableaux → impossible

3. **Binary Search :**
   - Si `canSplit(mid)` → essayer plus petit (`right = mid`)
   - Sinon → augmenter (`left = mid + 1`)
   - Template "find first" pour le minimum valide

**Exemple de trace pour `nums = [7,2,5,10,8], k = 2` :**

```
left=10, right=32

mid=21, canSplit([7,2,5,10,8], 2, 21) ?
- [7,2,5] sum=14 ≤ 21 ✓
- [10,8] sum=18 ≤ 21 ✓
- 2 sous-tableaux ≤ 2 ✓ → right=21

mid=15, canSplit([7,2,5,10,8], 2, 15) ?
- [7,2,5] sum=14 ≤ 15 ✓
- 10 > 15-0, nouveau : [10] sum=10 ≤ 15 ✓
- 8 ≤ 15-10, continuer : [10,8] sum=18 > 15 ✗
- Recommencer : [10], [8] → 3 sous-tableaux > 2 ✗ → left=16

...continuer jusqu'à left=right=18
```

**Complexité :**
- Temps : O(n × log(sum)) où n = len(nums)
- Espace : O(1)

**Points clés :**
- Binary search sur l'espace des réponses
- Algorithme greedy pour vérifier la faisabilité
- Propriété monotone : si score S fonctionne, S+1 fonctionne aussi

</details>

## 🚀 Exercices similaires

- [Koko Eating Bananas](../medium/koko-bananas.md) - Même pattern de base
- [Capacity to Ship Packages](../medium/ship-packages.md) - Binary search sur capacité
- [Minimize Max Distance to Gas Station](../hard/gas-station.md) - Plus avancé

## 🎓 Concepts clés

- **Binary Search on Answer Space** : chercher dans l'espace des solutions
- **Algorithme greedy de validation** : stratégie simple pour vérifier la faisabilité
- **Propriété monotone** : si solution S valide, S+1 l'est aussi
- **Optimisation de contraintes** : minimiser le maximum