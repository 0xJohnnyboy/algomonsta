# Exercice - Maximum Average Subarray I

**Niveau :** Easy
**Pattern :** Sliding Window
**Retour :** [📚 Cours Sliding Window](../../courses/02-sliding-window.md)

## 📝 Énoncé

Vous avez un tableau d'entiers `nums` composé de `n` éléments, et un entier `k`.

Trouvez un sous-tableau contigu dont la longueur est égale à `k` qui a la moyenne maximale et retournez cette valeur. Toute réponse avec une erreur de calcul inférieure à `10^-5` sera acceptée.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [1,12,-5,-6,50,3], k = 4
Output: 12.75000
Explication: Moyenne maximale est (12 - 5 - 6 + 50) / 4 = 51 / 4 = 12.75
```

**Exemple 2 :**
```
Input: nums = [5], k = 1
Output: 5.00000
```

## 🎯 Contraintes

- `n == nums.length`
- `1 <= k <= n <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez une fenêtre glissante de taille `k`. Calculez la somme de la première fenêtre, puis faites-la glisser.

</details>

<details>
<summary>Indice 2</summary>

Pour faire glisser la fenêtre, soustrayez l'élément qui sort et ajoutez l'élément qui entre.

</details>

<details>
<summary>Indice 3</summary>

Gardez la trace de la somme maximale trouvée, puis divisez par `k` à la fin pour obtenir la moyenne.

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "math"
)

func findMaxAverage(nums []int, k int) float64 {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        nums     []int
        k        int
        expected float64
    }{
        {[]int{1, 12, -5, -6, 50, 3}, 4, 12.75},
        {[]int{5}, 1, 5.0},
        {[]int{0, 1, 1, 3, 3}, 4, 2.0},
        {[]int{4, 0, 4, 3, 3}, 5, 2.8},
        {[]int{-1}, 1, -1.0},
        {[]int{1, 2, 3, 4}, 2, 3.5},
        {[]int{9, 7, 3, 5, 6, 9, 8, 5, 6, 9}, 6, 7.0},
    }

    fmt.Println("=== Tests Maximum Average Subarray ===")
    for i, test := range tests {
        result := findMaxAverage(test.nums, test.k)
        status := "✅"
        if math.Abs(result-test.expected) > 1e-5 {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: nums=%v, k=%d\n", test.nums, test.k)
        fmt.Printf("  Expected: %.5f, Got: %.5f\n\n", test.expected, result)
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func findMaxAverage(nums []int, k int) float64 {
    // Calculer la somme de la première fenêtre
    sum := 0
    for i := 0; i < k; i++ {
        sum += nums[i]
    }

    maxSum := sum

    // Faire glisser la fenêtre
    for i := k; i < len(nums); i++ {
        // Retirer l'élément qui sort et ajouter celui qui entre
        sum = sum - nums[i-k] + nums[i]
        if sum > maxSum {
            maxSum = sum
        }
    }

    return float64(maxSum) / float64(k)
}
```

**Explication :**
1. Calculez la somme de la première fenêtre de taille `k`
2. Faites glisser la fenêtre en soustrayant l'élément qui sort et en ajoutant l'élément qui entre
3. Gardez la trace de la somme maximale
4. Retournez la moyenne maximale en divisant par `k`

**Complexité :**
- Temps : O(n) - un seul passage à travers le tableau
- Espace : O(1) - seules quelques variables utilisées

</details>

## 🚀 Exercices similaires

- [Longest Substring Without Repeating Characters](../medium/longest-substring.md)
- [Contains Duplicate](contains-duplicate.md)
- [Max Consecutive Ones](../medium/max-ones.md)