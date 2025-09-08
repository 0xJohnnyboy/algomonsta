# Exercice - Two Sum II (Input Array Is Sorted)

**Niveau :** Easy  
**Pattern :** Two Pointers  
**Retour au cours :** [Two Pointers Pattern](../../courses/01-two-pointers.md)

## 📝 Énoncé

Étant donné un tableau d'entiers `numbers` **trié par ordre croissant**, trouvez deux nombres tels que leur somme soit égale à `target`. Retournez les **indices** de ces deux nombres sous forme d'un tableau d'entiers de taille 2, où le premier indice est plus petit que le second.

**Note :** Les indices sont **1-indexés** (commencent à 1, pas 0).

Il est garanti qu'il existe exactement une solution.

## 🔍 Exemples

**Exemple 1 :**
```
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explication: La somme de numbers[0] et numbers[1] est 2 + 7 = 9
Donc nous retournons [1, 2] (1-indexé)
```

**Exemple 2 :**
```
Input: numbers = [2,3,4], target = 6  
Output: [1,3]
```

**Exemple 3 :**
```
Input: numbers = [-1,0], target = -1
Output: [1,2]
```

## 🎯 Contraintes

- `2 <= numbers.length <= 3 * 10^4`
- `-1000 <= numbers[i] <= 1000`
- `numbers` est trié par **ordre croissant**
- `-1000 <= target <= 1000`
- Il existe **exactement une solution**

## 💡 Indices

<details>
<summary>Indice 1</summary>

Puisque le tableau est trié, utilisez deux pointeurs : un au début et un à la fin.

</details>

<details>
<summary>Indice 2</summary>

Si la somme est trop petite, bougez le pointeur de gauche vers la droite.  
Si la somme est trop grande, bougez le pointeur de droite vers la gauche.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func twoSum(numbers []int, target int) []int {
    // Votre code ici
    
}

func main() {
    // Test cases
    fmt.Println(twoSum([]int{2, 7, 11, 15}, 9))    // Expected: [1, 2]
    fmt.Println(twoSum([]int{2, 3, 4}, 6))         // Expected: [1, 3]  
    fmt.Println(twoSum([]int{-1, 0}, -1))          // Expected: [1, 2]
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func twoSum(numbers []int, target int) []int {
    left, right := 0, len(numbers)-1
    
    for left < right {
        sum := numbers[left] + numbers[right]
        
        if sum == target {
            return []int{left + 1, right + 1} // 1-indexé
        } else if sum < target {
            left++ // Augmenter la somme
        } else {
            right-- // Diminuer la somme
        }
    }
    
    // Cette ligne ne devrait jamais être atteinte selon les contraintes
    return []int{}
}
```

**Explication :**
1. On place deux pointeurs aux extrémités du tableau
2. Si la somme est égale au target, on a trouvé la réponse
3. Si la somme est trop petite, on augmente le pointeur gauche
4. Si la somme est trop grande, on diminue le pointeur droite
5. On continue jusqu'à trouver la solution

**Complexité :**
- Temps : O(n) - un seul passage
- Espace : O(1) - pas d'espace supplémentaire

</details>

## 🚀 Exercices similaires

- [3Sum](../medium/three-sum.md) - Extension avec 3 nombres
- [4Sum](../hard/four-sum.md) - Extension avec 4 nombres  
- [Two Sum (Unsorted)](../easy/two-sum-unsorted.md) - Version avec HashMap
