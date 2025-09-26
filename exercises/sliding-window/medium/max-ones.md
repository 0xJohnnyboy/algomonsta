# Exercice - Max Consecutive Ones III

**Niveau :** Medium
**Pattern :** Sliding Window
**Retour :** [📚 Cours Sliding Window](../../courses/02-sliding-window.md)

## 📝 Énoncé

Étant donné un tableau binaire `nums` et un entier `k`, retournez le nombre maximum de 1 consécutifs dans le tableau si vous pouvez retourner au plus `k` zéros.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [1,1,1,0,0,0,1,1,1,1,0], k = 2
Output: 6
Explication: [1,1,1,0,0,1,1,1,1,1,0]
Les nombres en gras ont été retournés de 0 à 1. La longueur maximale est 6.
```

**Exemple 2 :**
```
Input: nums = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], k = 3
Output: 10
Explication: [0,0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,1]
Les nombres en gras ont été retournés de 0 à 1. La longueur maximale est 10.
```

## 🎯 Contraintes

- `1 <= nums.length <= 10^5`
- `nums[i]` est soit `0` soit `1`
- `0 <= k <= nums.length`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez une fenêtre glissante avec deux pointeurs. Maintenez un compteur de zéros dans la fenêtre courante.

</details>

<details>
<summary>Indice 2</summary>

Étendez la fenêtre vers la droite tant que le nombre de zéros est <= k.

</details>

<details>
<summary>Indice 3</summary>

Quand le nombre de zéros dépasse k, rétrécissez la fenêtre depuis la gauche jusqu'à ce que le nombre de zéros redevienne <= k.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func longestOnes(nums []int, k int) int {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        nums     []int
        k        int
        expected int
    }{
        {[]int{1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 0}, 2, 6},
        {[]int{0, 0, 1, 1, 0, 0, 1, 1, 1, 0, 1, 1, 0, 0, 0, 1, 1, 1, 1}, 3, 10},
        {[]int{1, 1, 1, 1}, 0, 4},
        {[]int{0, 0, 0, 0}, 2, 2},
        {[]int{1, 0, 1, 0, 1}, 1, 3},
        {[]int{0, 0, 0, 1}, 4, 4},
        {[]int{1, 1, 0, 1}, 1, 4},
        {[]int{0}, 1, 1},
        {[]int{1}, 0, 1},
    }

    fmt.Println("=== Tests Max Consecutive Ones III ===")
    for i, test := range tests {
        result := longestOnes(test.nums, test.k)
        status := "✅"
        if result != test.expected {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: nums=%v, k=%d\n", test.nums, test.k)
        fmt.Printf("  Expected: %d, Got: %d\n\n", test.expected, result)
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func longestOnes(nums []int, k int) int {
    left := 0
    zeroCount := 0
    maxLength := 0

    for right := 0; right < len(nums); right++ {
        // Si on rencontre un 0, incrémenter le compteur
        if nums[right] == 0 {
            zeroCount++
        }

        // Si on a trop de zéros, rétrécir la fenêtre depuis la gauche
        for zeroCount > k {
            if nums[left] == 0 {
                zeroCount--
            }
            left++
        }

        // Mettre à jour la longueur maximale
        currentLength := right - left + 1
        if currentLength > maxLength {
            maxLength = currentLength
        }
    }

    return maxLength
}
```

**Explication :**
1. Utilisez deux pointeurs pour maintenir une fenêtre glissante
2. Comptez le nombre de zéros dans la fenêtre courante
3. Étendez la fenêtre vers la droite tant que le nombre de zéros <= k
4. Quand le nombre de zéros dépasse k, rétrécissez depuis la gauche
5. Gardez la trace de la longueur maximale de fenêtre valide

**Complexité :**
- Temps : O(n) - chaque élément est visité au plus deux fois
- Espace : O(1) - seulement quelques variables utilisées

</details>

## 🚀 Exercices similaires

- [Longest Substring Without Repeating Characters](longest-substring.md)
- [Fruit Into Baskets](fruit-baskets.md)
- [Contains Duplicate II](../easy/contains-duplicate.md)