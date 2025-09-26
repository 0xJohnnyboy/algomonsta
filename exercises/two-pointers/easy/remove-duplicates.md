# Exercice - Supprimer les Doublons du Tableau Trié

**Niveau :** Easy
**Pattern :** Two Pointers
**Retour :** [📚 Cours Two Pointers](../../courses/01-two-pointers.md)

## 📝 Énoncé

Étant donné un tableau d'entiers `nums` trié par ordre croissant, supprimez les doublons **en place** de sorte que chaque élément unique n'apparaisse qu'**une seule fois**. L'ordre relatif des éléments doit être conservé. Puis retournez le nombre d'éléments uniques dans `nums`.

Considérez le nombre d'éléments uniques de `nums` comme `k`, pour être accepté, vous devez faire ce qui suit :

- Modifiez le tableau `nums` de sorte que les `k` premiers éléments de `nums` contiennent les éléments uniques dans l'ordre où ils étaient présents dans `nums` à l'origine.
- Les éléments restants de `nums` ne sont pas importants ainsi que la taille de `nums`.
- Retournez `k`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [1,1,2]
Output: 2
Explication: Votre fonction doit retourner k = 2, avec les deux premiers éléments de nums étant 1 et 2 respectivement.
Peu importe ce que vous laissez au-delà du k retourné (d'où ils sont des traits de soulignement).
```

**Exemple 2 :**
```
Input: nums = [0,0,1,1,1,2,2,3,3,4]
Output: 5
Explication: Votre fonction doit retourner k = 5, avec les cinq premiers éléments de nums étant 0, 1, 2, 3, et 4 respectivement.
Peu importe ce que vous laissez au-delà du k retourné (d'où ils sont des traits de soulignement).
```

## 🎯 Contraintes

- `1 <= nums.length <= 3 * 10^4`
- `-100 <= nums[i] <= 100`
- `nums` est trié par ordre croissant.

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez deux pointeurs : un pour parcourir le tableau et un autre pour marquer la position du prochain élément unique.

</details>

<details>
<summary>Indice 2</summary>

Commencez avec le pointeur de position à l'index 1, car le premier élément est toujours unique.

</details>

<details>
<summary>Indice 3</summary>

Quand vous trouvez un élément différent du précédent, placez-le à la position du pointeur et avancez le pointeur.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func removeDuplicates(nums []int) int {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        input    []int
        expected int
    }{
        {[]int{1, 1, 2}, 2},
        {[]int{0, 0, 1, 1, 1, 2, 2, 3, 3, 4}, 5},
        {[]int{1}, 1},
        {[]int{1, 2}, 2},
        {[]int{1, 1, 1, 1}, 1},
        {[]int{1, 2, 3, 4, 5}, 5},
        {[]int{-3, -1, 0, 0, 0, 3, 3}, 4},
    }

    fmt.Println("=== Tests Supprimer Doublons ===")
    for i, test := range tests {
        // Copier l'input pour les tests
        nums := make([]int, len(test.input))
        copy(nums, test.input)

        result := removeDuplicates(nums)
        status := "✅"
        if result != test.expected {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: %v\n", test.input)
        fmt.Printf("  Expected: %d, Got: %d\n", test.expected, result)
        fmt.Printf("  Tableau modifié (premiers %d éléments): %v\n\n", result, nums[:result])
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func removeDuplicates(nums []int) int {
    if len(nums) <= 1 {
        return len(nums)
    }

    // Pointeur pour la position du prochain élément unique
    uniquePos := 1

    // Parcourir le tableau à partir du deuxième élément
    for i := 1; i < len(nums); i++ {
        // Si l'élément actuel est différent du précédent
        if nums[i] != nums[i-1] {
            nums[uniquePos] = nums[i]
            uniquePos++
        }
    }

    return uniquePos
}
```

**Explication :**
1. Gérez les cas limites (tableau vide ou avec un seul élément)
2. Initialisez `uniquePos` à 1 car le premier élément est toujours unique
3. Parcourez le tableau et comparez chaque élément avec le précédent
4. Si différent, placez l'élément à la position `uniquePos` et incrémentez
5. Retournez le nombre d'éléments uniques

**Complexité :**
- Temps : O(n) - un seul passage à travers le tableau
- Espace : O(1) - modification en place, seuls quelques pointeurs utilisés

</details>

## 🚀 Exercices similaires

- [Two Sum II](two-sum-ii.md)
- [Valid Palindrome](valid-palindrome.md)
- [Three Sum](../medium/three-sum.md)