# Exercice - Three Sum

**Niveau :** Medium
**Pattern :** Two Pointers
**Retour :** [📚 Cours Two Pointers](../../courses/01-two-pointers.md)

## 📝 Énoncé

Étant donné un tableau d'entiers `nums`, retournez tous les triplets `[nums[i], nums[j], nums[k]]` tels que `i != j`, `i != k`, et `j != k`, et `nums[i] + nums[j] + nums[k] == 0`.

Notez que l'ensemble de solutions ne doit pas contenir de triplets en double.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]
Explication:
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0.
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0.
Les triplets distincts sont [-1,0,1] et [-1,-1,2].
Notez que l'ordre des triplets et l'ordre à l'intérieur des triplets n'a pas d'importance.
```

**Exemple 2 :**
```
Input: nums = [0,1,1]
Output: []
Explication: Le seul triplet possible n'additionne pas à 0.
```

**Exemple 3 :**
```
Input: nums = [0,0,0]
Output: [[0,0,0]]
Explication: Le seul triplet possible additionne à 0.
```

## 🎯 Contraintes

- `3 <= nums.length <= 3000`
- `-10^5 <= nums[i] <= 10^5`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Triez d'abord le tableau. Cela permettra d'éviter les doublons plus facilement et d'utiliser la technique des two pointers.

</details>

<details>
<summary>Indice 2</summary>

Pour chaque élément, utilisez two pointers sur le reste du tableau pour trouver les paires qui complètent la somme à 0.

</details>

<details>
<summary>Indice 3</summary>

Évitez les doublons en sautant les éléments identiques consécutifs à chaque niveau (élément fixe et pointeurs).

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "sort"
)

func threeSum(nums []int) [][]int {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        input    []int
        expected [][]int
    }{
        {
            []int{-1, 0, 1, 2, -1, -4},
            [][]int{{-1, -1, 2}, {-1, 0, 1}},
        },
        {
            []int{0, 1, 1},
            [][]int{},
        },
        {
            []int{0, 0, 0},
            [][]int{{0, 0, 0}},
        },
        {
            []int{-2, 0, 1, 1, 2},
            [][]int{{-2, 0, 2}, {-2, 1, 1}},
        },
        {
            []int{1, 2, -2, -1},
            [][]int{},
        },
        {
            []int{-1, 0, 1, 0},
            [][]int{{-1, 0, 1}},
        },
        {
            []int{1, -1, -1, 0},
            [][]int{{-1, 0, 1}},
        },
    }

    fmt.Println("=== Tests Three Sum ===")
    for i, test := range tests {
        result := threeSum(test.input)
        status := "✅"
        if !equalTriplets(result, test.expected) {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: %v\n", test.input)
        fmt.Printf("  Expected: %v\n", test.expected)
        fmt.Printf("  Got: %v\n\n", result)
    }
}

// Fonction utilitaire pour comparer les triplets (ordre indépendant)
func equalTriplets(a, b [][]int) bool {
    if len(a) != len(b) {
        return false
    }

    // Conversion en map pour comparaison
    mapA := make(map[string]bool)
    for _, triplet := range a {
        sort.Ints(triplet)
        key := fmt.Sprintf("%d,%d,%d", triplet[0], triplet[1], triplet[2])
        mapA[key] = true
    }

    mapB := make(map[string]bool)
    for _, triplet := range b {
        sort.Ints(triplet)
        key := fmt.Sprintf("%d,%d,%d", triplet[0], triplet[1], triplet[2])
        mapB[key] = true
    }

    for key := range mapA {
        if !mapB[key] {
            return false
        }
    }

    return true
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func threeSum(nums []int) [][]int {
    sort.Ints(nums)
    result := [][]int{}

    for i := 0; i < len(nums)-2; i++ {
        // Éviter les doublons pour le premier élément
        if i > 0 && nums[i] == nums[i-1] {
            continue
        }

        left := i + 1
        right := len(nums) - 1

        for left < right {
            sum := nums[i] + nums[left] + nums[right]

            if sum == 0 {
                result = append(result, []int{nums[i], nums[left], nums[right]})

                // Éviter les doublons pour les deux autres éléments
                for left < right && nums[left] == nums[left+1] {
                    left++
                }
                for left < right && nums[right] == nums[right-1] {
                    right--
                }

                left++
                right--
            } else if sum < 0 {
                left++
            } else {
                right--
            }
        }
    }

    return result
}
```

**Explication :**
1. Triez le tableau pour faciliter l'évitement des doublons et l'utilisation des two pointers
2. Fixez le premier élément et utilisez two pointers sur le reste
3. Si la somme est 0, ajoutez le triplet et évitez les doublons
4. Si la somme est trop petite, augmentez le pointeur gauche
5. Si la somme est trop grande, diminuez le pointeur droit

**Complexité :**
- Temps : O(n²) - O(n log n) pour le tri + O(n²) pour les nested loops
- Espace : O(1) - sans compter l'espace pour le résultat

</details>

## 🚀 Exercices similaires

- [Two Sum II](../easy/two-sum-ii.md)
- [Container With Most Water](container-water.md)
- [Trapping Rain Water](../hard/trapping-rain-water.md)