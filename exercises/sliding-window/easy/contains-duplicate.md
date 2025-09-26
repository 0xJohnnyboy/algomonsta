# Exercice - Contains Duplicate II

**Niveau :** Easy
**Pattern :** Sliding Window
**Retour :** [📚 Cours Sliding Window](../../courses/02-sliding-window.md)

## 📝 Énoncé

Étant donné un tableau d'entiers `nums` et un entier `k`, retournez `true` s'il existe deux indices distincts `i` et `j` dans le tableau tels que `nums[i] == nums[j]` et `abs(i - j) <= k`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [1,2,3,1], k = 3
Output: true
Explication: Les éléments à l'index 0 et 3 sont égaux et abs(0-3) = 3 <= 3
```

**Exemple 2 :**
```
Input: nums = [1,0,1,1], k = 1
Output: true
Explication: Les éléments à l'index 2 et 3 sont égaux et abs(2-3) = 1 <= 1
```

**Exemple 3 :**
```
Input: nums = [1,2,3,1,2,3], k = 2
Output: false
Explication: Aucune paire d'éléments égaux n'est à une distance <= 2
```

## 🎯 Contraintes

- `1 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- `0 <= k <= 10^5`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez une fenêtre glissante de taille `k+1`. Maintenez un set des éléments dans la fenêtre courante.

</details>

<details>
<summary>Indice 2</summary>

Si vous trouvez un élément déjà présent dans le set, vous avez trouvé une paire valide.

</details>

<details>
<summary>Indice 3</summary>

Quand la fenêtre dépasse la taille `k+1`, retirez l'élément le plus ancien du set.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func containsNearbyDuplicate(nums []int, k int) bool {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        nums     []int
        k        int
        expected bool
    }{
        {[]int{1, 2, 3, 1}, 3, true},
        {[]int{1, 0, 1, 1}, 1, true},
        {[]int{1, 2, 3, 1, 2, 3}, 2, false},
        {[]int{99, 99}, 2, true},
        {[]int{1, 2, 1}, 0, false},
        {[]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 9}, 3, true},
        {[]int{4, 1, 2, 3, 4}, 3, false},
        {[]int{1}, 1, false},
    }

    fmt.Println("=== Tests Contains Duplicate II ===")
    for i, test := range tests {
        result := containsNearbyDuplicate(test.nums, test.k)
        status := "✅"
        if result != test.expected {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: nums=%v, k=%d\n", test.nums, test.k)
        fmt.Printf("  Expected: %t, Got: %t\n\n", test.expected, result)
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func containsNearbyDuplicate(nums []int, k int) bool {
    if k == 0 {
        return false
    }

    seen := make(map[int]bool)

    for i, num := range nums {
        // Si on a déjà vu ce nombre dans la fenêtre courante
        if seen[num] {
            return true
        }

        // Ajouter le nombre courant au set
        seen[num] = true

        // Si la fenêtre devient trop grande, retirer l'élément le plus ancien
        if i >= k {
            delete(seen, nums[i-k])
        }
    }

    return false
}
```

**Explication :**
1. Utilisez une map pour maintenir les éléments dans une fenêtre glissante de taille `k+1`
2. Pour chaque élément, vérifiez s'il existe déjà dans la fenêtre
3. Si oui, retournez `true` (paire trouvée)
4. Sinon, ajoutez l'élément à la map
5. Si la fenêtre dépasse la taille `k`, retirez l'élément le plus ancien

**Complexité :**
- Temps : O(n) - un seul passage à travers le tableau
- Espace : O(min(n, k)) - au maximum k+1 éléments dans la map

</details>

## 🚀 Exercices similaires

- [Maximum Average Subarray I](max-average.md)
- [Longest Substring Without Repeating Characters](../medium/longest-substring.md)
- [Max Consecutive Ones III](../medium/max-ones.md)