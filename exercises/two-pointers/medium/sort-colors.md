# Exercice - Sort Colors (Dutch National Flag)

**Niveau :** Medium
**Pattern :** Two Pointers
**Retour :** [📚 Cours Two Pointers](../../courses/01-two-pointers.md)

## 📝 Énoncé

Étant donné un tableau `nums` avec `n` objets colorés en rouge, blanc ou bleu, triez-les **en place** de sorte que les objets de la même couleur soient adjacents, avec les couleurs dans l'ordre rouge, blanc et bleu.

Nous utiliserons les entiers `0`, `1`, et `2` pour représenter les couleurs rouge, blanche et bleue, respectivement.

Vous devez résoudre ce problème sans utiliser la fonction de tri de la bibliothèque.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

**Exemple 2 :**
```
Input: nums = [2,0,1]
Output: [0,1,2]
```

## 🎯 Contraintes

- `n == nums.length`
- `1 <= n <= 300`
- `nums[i]` is either `0`, `1`, or `2`.

**Follow up :** Pouvez-vous résoudre ce problème en un seul passage ?
**Follow up :** Pouvez-vous le faire avec un algorithme à temps constant O(1) d'espace supplémentaire ?

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez trois pointeurs : un pour les 0s, un pour explorer, et un pour les 2s.

</details>

<details>
<summary>Indice 2</summary>

L'algorithme du "Drapeau Néerlandais" utilise trois pointeurs pour partitionner en trois zones.

</details>

<details>
<summary>Indice 3</summary>

Quand vous voyez un 0, échangez-le avec la position des 0s. Quand vous voyez un 2, échangez-le avec la position des 2s.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func sortColors(nums []int) {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        input    []int
        expected []int
    }{
        {[]int{2, 0, 2, 1, 1, 0}, []int{0, 0, 1, 1, 2, 2}},
        {[]int{2, 0, 1}, []int{0, 1, 2}},
        {[]int{0}, []int{0}},
        {[]int{1}, []int{1}},
        {[]int{2}, []int{2}},
        {[]int{1, 2, 0}, []int{0, 1, 2}},
        {[]int{2, 2, 2, 0, 0, 0, 1, 1}, []int{0, 0, 0, 1, 1, 2, 2, 2}},
        {[]int{1, 0, 1, 0, 1, 0}, []int{0, 0, 0, 1, 1, 1}},
    }

    fmt.Println("=== Tests Sort Colors ===")
    for i, test := range tests {
        // Copier l'input pour les tests
        nums := make([]int, len(test.input))
        copy(nums, test.input)

        sortColors(nums)
        status := "✅"
        if !equalSlices(nums, test.expected) {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: %v\n", test.input)
        fmt.Printf("  Expected: %v\n", test.expected)
        fmt.Printf("  Got: %v\n\n", nums)
    }
}

// Fonction utilitaire pour comparer les slices
func equalSlices(a, b []int) bool {
    if len(a) != len(b) {
        return false
    }
    for i := range a {
        if a[i] != b[i] {
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
func sortColors(nums []int) {
    left := 0      // Pointeur pour les 0s
    right := len(nums) - 1  // Pointeur pour les 2s
    current := 0   // Pointeur d'exploration

    for current <= right {
        if nums[current] == 0 {
            // Échanger avec la zone des 0s
            nums[left], nums[current] = nums[current], nums[left]
            left++
            current++
        } else if nums[current] == 2 {
            // Échanger avec la zone des 2s
            nums[right], nums[current] = nums[current], nums[right]
            right--
            // Ne pas incrémenter current car on doit vérifier l'élément échangé
        } else {
            // C'est un 1, il est déjà à sa place
            current++
        }
    }
}
```

**Explication :**
1. **Zone des 0s** : `[0, left)` - tous les éléments avant `left` sont des 0s
2. **Zone des 1s** : `[left, current)` - tous les éléments entre `left` et `current` sont des 1s
3. **Zone inexplorée** : `[current, right]` - éléments pas encore traités
4. **Zone des 2s** : `(right, end]` - tous les éléments après `right` sont des 2s

Quand on trouve un 0, on l'échange avec la position `left` et on avance les deux pointeurs.
Quand on trouve un 2, on l'échange avec la position `right` mais on n'avance pas `current` car on doit vérifier l'élément qu'on vient de recevoir.

**Complexité :**
- Temps : O(n) - un seul passage à travers le tableau
- Espace : O(1) - tri en place

</details>

## 🚀 Exercices similaires

- [Remove Duplicates](../easy/remove-duplicates.md)
- [Three Sum](three-sum.md)
- [Container With Most Water](container-water.md)