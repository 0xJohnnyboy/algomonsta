# Exercice - Search Insert Position

**Niveau :** Easy
**Pattern :** Binary Search for Insert Position
**Retour au cours :** [Binary Search Pattern](../../courses/03-binary-search.md)

## 📝 Énoncé

Étant donné un tableau d'entiers distincts `nums` trié par **ordre croissant** et un entier `target`, retournez l'index où `target` doit être inséré pour que le tableau reste trié.

Vous devez écrire un algorithme avec une complexité temporelle de `O(log n)`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [1,3,5,6], target = 5
Output: 2
Explication: 5 existe déjà à l'index 2
```

**Exemple 2 :**
```
Input: nums = [1,3,5,6], target = 2
Output: 1
Explication: 2 doit être inséré à l'index 1 pour garder l'ordre
```

**Exemple 3 :**
```
Input: nums = [1,3,5,6], target = 7
Output: 4
Explication: 7 doit être inséré à la fin du tableau
```

**Exemple 4 :**
```
Input: nums = [1,3,5,6], target = 0
Output: 0
Explication: 0 doit être inséré au début du tableau
```

## 🎯 Contraintes

- `1 <= nums.length <= 10^4`
- `-10^4 <= nums[i] <= 10^4`
- `nums` contient des valeurs **distinctes** triées par ordre croissant
- `-10^4 <= target <= 10^4`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est une variante de binary search où on cherche la position d'insertion plutôt que l'élément lui-même.

</details>

<details>
<summary>Indice 2</summary>

Si l'élément existe, retournez son index. Sinon, retournez l'index où il devrait être inséré.

</details>

<details>
<summary>Indice 3</summary>

Pensez à ce qui se passe quand la boucle se termine. Où pointe `left` ?

</details>

<details>
<summary>Indice 4</summary>

La position d'insertion est l'index du premier élément qui est **supérieur ou égal** à target.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func searchInsert(nums []int, target int) int {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println(searchInsert([]int{1, 3, 5, 6}, 5))    // Expected: 2
    fmt.Println(searchInsert([]int{1, 3, 5, 6}, 2))    // Expected: 1
    fmt.Println(searchInsert([]int{1, 3, 5, 6}, 7))    // Expected: 4
    fmt.Println(searchInsert([]int{1, 3, 5, 6}, 0))    // Expected: 0
    fmt.Println(searchInsert([]int{1}, 0))              // Expected: 0
    fmt.Println(searchInsert([]int{1}, 1))              // Expected: 0
    fmt.Println(searchInsert([]int{1}, 2))              // Expected: 1
    fmt.Println(searchInsert([]int{2, 4, 6, 8}, 3))    // Expected: 1
    fmt.Println(searchInsert([]int{2, 4, 6, 8}, 5))    // Expected: 2
    fmt.Println(searchInsert([]int{1, 3, 5}, 4))       // Expected: 2
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func searchInsert(nums []int, target int) int {
    left, right := 0, len(nums)-1

    for left <= right {
        mid := left + (right-left)/2

        if nums[mid] == target {
            return mid
        } else if nums[mid] < target {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }

    // À la fin de la boucle, left est la position d'insertion
    return left
}
```

**Explication détaillée :**

1. **Même logique que binary search classique :**
   - Utilise les mêmes pointeurs `left` et `right`
   - Même condition de boucle `left <= right`

2. **Cas où target existe :**
   - Si `nums[mid] == target`, retourner `mid` directement

3. **Cas où target n'existe pas :**
   - À la fin de la boucle, `left > right`
   - `left` pointe vers la position où target devrait être inséré

4. **Pourquoi `left` est la bonne réponse ?**
   - Quand `nums[mid] < target` : on va à droite (`left = mid + 1`)
   - Quand `nums[mid] > target` : on va à gauche (`right = mid - 1`)
   - À la fin, `left` pointe vers le premier élément >= target

**Exemple de trace pour `nums = [1,3,5,6], target = 2` :**

```
Début: left=0, right=3
mid=1, nums[1]=3 > 2 → right=0
mid=0, nums[0]=1 < 2 → left=1
left > right → sortie, retourner left=1
```

**Complexité :**
- Temps : O(log n)
- Espace : O(1)

**Points clés :**
- Même template que binary search classique
- La position d'insertion est automatiquement dans `left` à la fin
- Gère tous les cas : début, milieu, fin, élément existant

</details>

## 🚀 Exercices similaires

- [Binary Search](binary-search.md) - Version de base
- [First Bad Version](first-bad-version.md) - Même pattern de recherche
- [Find First and Last Position](../medium/find-range.md) - Extension du concept

## 🎓 Concepts clés

- **Position d'insertion** : premier index où target peut être placé
- **Invariant de fin** : `left` pointe vers la position correcte
- **Binary Search adaptatif** : même algorithme, interprétation différente
- **Gestion des cas limites** : début, fin, élément unique