# Exercice - Sliding Window Maximum

**Niveau :** Hard
**Pattern :** Sliding Window
**Retour :** [📚 Cours Sliding Window](../../courses/02-sliding-window.md)

## 📝 Énoncé

Vous recevez un tableau d'entiers `nums`, il y a une fenêtre glissante de taille `k` qui se déplace de l'extrême gauche du tableau à l'extrême droite. Vous ne pouvez voir que les `k` nombres dans la fenêtre. Chaque fois que la fenêtre glissante se déplace d'une position vers la droite.

Retournez le maximum de la fenêtre glissante.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explication:
Position de la fenêtre                Maximum
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**Exemple 2 :**
```
Input: nums = [1], k = 1
Output: [1]
```

## 🎯 Contraintes

- `1 <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`
- `1 <= k <= nums.length`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Une approche naïve O(nk) consisterait à calculer le maximum pour chaque fenêtre. Pouvez-vous faire mieux ?

</details>

<details>
<summary>Indice 2</summary>

Utilisez une deque (double-ended queue) pour maintenir les indices des éléments dans un ordre décroissant de leurs valeurs.

</details>

<details>
<summary>Indice 3</summary>

La deque stockera les indices des éléments de manière à ce que le front contienne toujours l'indice de l'élément maximum dans la fenêtre courante.

</details>

<details>
<summary>Indice 4</summary>

Avant d'ajouter un nouvel élément, retirez tous les éléments plus petits de l'arrière de la deque, et retirez les éléments hors de la fenêtre du front.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func maxSlidingWindow(nums []int, k int) []int {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        nums     []int
        k        int
        expected []int
    }{
        {[]int{1, 3, -1, -3, 5, 3, 6, 7}, 3, []int{3, 3, 5, 5, 6, 7}},
        {[]int{1}, 1, []int{1}},
        {[]int{1, -1}, 1, []int{1, -1}},
        {[]int{9, 11}, 2, []int{11}},
        {[]int{4, -2}, 2, []int{4}},
        {[]int{1, 3, 1, 2, 0, 5}, 3, []int{3, 3, 2, 5}},
        {[]int{7, 2, 4}, 2, []int{7, 4}},
        {[]int{1, 2, 3, 4, 5}, 3, []int{3, 4, 5}},
    }

    fmt.Println("=== Tests Sliding Window Maximum ===")
    for i, test := range tests {
        result := maxSlidingWindow(test.nums, test.k)
        status := "✅"
        if len(result) != len(test.expected) {
            status = "❌"
        } else {
            for j := 0; j < len(result); j++ {
                if result[j] != test.expected[j] {
                    status = "❌"
                    break
                }
            }
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: nums=%v, k=%d\n", test.nums, test.k)
        fmt.Printf("  Expected: %v, Got: %v\n\n", test.expected, result)
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func maxSlidingWindow(nums []int, k int) []int {
    if len(nums) == 0 || k == 0 {
        return []int{}
    }

    // Deque pour stocker les indices
    deque := make([]int, 0)
    result := make([]int, 0)

    for i := 0; i < len(nums); i++ {
        // Retirer les indices qui sont hors de la fenêtre courante
        for len(deque) > 0 && deque[0] <= i-k {
            deque = deque[1:]
        }

        // Retirer tous les éléments plus petits que l'élément courant
        // de l'arrière de la deque
        for len(deque) > 0 && nums[deque[len(deque)-1]] <= nums[i] {
            deque = deque[:len(deque)-1]
        }

        // Ajouter l'indice courant à l'arrière de la deque
        deque = append(deque, i)

        // Si on a traité au moins k éléments, ajouter le maximum à result
        if i >= k-1 {
            result = append(result, nums[deque[0]])
        }
    }

    return result
}
```

**Explication :**
1. Utilisez une deque pour maintenir les indices des éléments en ordre décroissant de valeurs
2. Pour chaque nouvel élément :
   - Retirez les indices hors de la fenêtre courante du front
   - Retirez tous les éléments plus petits de l'arrière (ils ne seront jamais maximum)
   - Ajoutez l'indice courant à l'arrière
3. Le front de la deque contient toujours l'indice de l'élément maximum de la fenêtre
4. Ajoutez le maximum au résultat une fois que la fenêtre est de taille k

**Complexité :**
- Temps : O(n) - chaque élément est ajouté et retiré au plus une fois de la deque
- Espace : O(k) - la deque contient au maximum k éléments

</details>

## 🚀 Exercices similaires

- [Minimum Window Substring](min-window.md)
- [Max Consecutive Ones III](../medium/max-ones.md)
- [Longest Substring Without Repeating Characters](../medium/longest-substring.md)