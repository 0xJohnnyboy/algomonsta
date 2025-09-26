# Exercice - Search in Rotated Sorted Array

**Niveau :** Medium
**Pattern :** Binary Search in Modified Array
**Retour au cours :** [Binary Search Pattern](../../courses/03-binary-search.md)

## 📝 Énoncé

Il y a un tableau d'entiers `nums` trié par ordre croissant (avec des valeurs **distinctes**).

Avant d'être passé à votre fonction, `nums` est possiblement **pivoté** à un index pivot `k` inconnu (`1 <= k < nums.length`) tel que le tableau résultant est `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (**indexé à partir de 0**). Par exemple, `[0,1,2,4,5,6,7]` pourrait être pivoté à l'index pivot `3` et devenir `[4,5,6,7,0,1,2]`.

Étant donné le tableau `nums` **après** le possible pivot et un entier `target`, retournez l'index de `target` s'il est dans `nums`, ou `-1` s'il n'y est pas.

Vous devez écrire un algorithme avec une complexité temporelle de `O(log n)`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
Explication: 0 se trouve à l'index 4
```

**Exemple 2 :**
```
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
Explication: 3 n'est pas dans le tableau
```

**Exemple 3 :**
```
Input: nums = [1], target = 0
Output: -1
```

**Exemple 4 :**
```
Input: nums = [1], target = 1
Output: 0
```

## 🎯 Contraintes

- `1 <= nums.length <= 5000`
- `-10^4 <= nums[i] <= 10^4`
- Toutes les valeurs de `nums` sont **uniques**
- `nums` est un tableau croissant qui est possiblement pivoté
- `-10^4 <= target <= 10^4`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Même si le tableau est pivoté, au moins une moitié (gauche ou droite) est toujours parfaitement triée.

</details>

<details>
<summary>Indice 2</summary>

À chaque étape de binary search, identifiez d'abord quelle moitié est triée en comparant `nums[left]` avec `nums[mid]`.

</details>

<details>
<summary>Indice 3</summary>

Une fois que vous savez quelle moitié est triée, vérifiez si le target se trouve dans cette moitié triée.

</details>

<details>
<summary>Indice 4</summary>

Si target est dans la moitié triée, cherchez là. Sinon, cherchez dans l'autre moitié.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func search(nums []int, target int) int {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println(search([]int{4, 5, 6, 7, 0, 1, 2}, 0))  // Expected: 4
    fmt.Println(search([]int{4, 5, 6, 7, 0, 1, 2}, 3))  // Expected: -1
    fmt.Println(search([]int{1}, 0))                      // Expected: -1
    fmt.Println(search([]int{1}, 1))                      // Expected: 0
    fmt.Println(search([]int{4, 5, 6, 7, 0, 1, 2}, 5))  // Expected: 1
    fmt.Println(search([]int{4, 5, 6, 7, 0, 1, 2}, 7))  // Expected: 3
    fmt.Println(search([]int{6, 7, 0, 1, 2, 4, 5}, 4))  // Expected: 5
    fmt.Println(search([]int{2, 3, 4, 5, 6, 7, 0, 1}, 6)) // Expected: 4
    fmt.Println(search([]int{3, 1}, 1))                  // Expected: 1
    fmt.Println(search([]int{5, 1, 3}, 3))               // Expected: 2
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func search(nums []int, target int) int {
    left, right := 0, len(nums)-1

    for left <= right {
        mid := left + (right-left)/2

        if nums[mid] == target {
            return mid
        }

        // Déterminer quelle moitié est triée
        if nums[left] <= nums[mid] {
            // Moitié gauche est triée
            if nums[left] <= target && target < nums[mid] {
                // Target est dans la moitié gauche triée
                right = mid - 1
            } else {
                // Target est dans la moitié droite
                left = mid + 1
            }
        } else {
            // Moitié droite est triée
            if nums[mid] < target && target <= nums[right] {
                // Target est dans la moitié droite triée
                left = mid + 1
            } else {
                // Target est dans la moitié gauche
                right = mid - 1
            }
        }
    }

    return -1
}
```

**Explication détaillée :**

1. **Observation clé :** Dans un tableau pivoté, au moins une moitié est toujours parfaitement triée

2. **Identifier la moitié triée :**
   - Si `nums[left] <= nums[mid]` → moitié gauche triée
   - Sinon → moitié droite triée

3. **Logique de recherche :**

   **Cas 1 : Moitié gauche triée**
   - Si `nums[left] <= target < nums[mid]` → target dans la partie triée
   - Chercher à gauche : `right = mid - 1`
   - Sinon → chercher à droite : `left = mid + 1`

   **Cas 2 : Moitié droite triée**
   - Si `nums[mid] < target <= nums[right]` → target dans la partie triée
   - Chercher à droite : `left = mid + 1`
   - Sinon → chercher à gauche : `right = mid - 1`

**Exemple de trace pour `nums = [4,5,6,7,0,1,2], target = 0` :**

```
Début: left=0, right=6
mid=3, nums[3]=7, nums[0]=4 <= nums[3] → gauche triée
target=0 ∉ [4,7) → chercher à droite, left=4

mid=5, nums[5]=1, nums[4]=0 <= nums[5] → gauche triée
target=0 ∈ [0,1) → chercher à gauche, right=4

mid=4, nums[4]=0 == target → retourner 4
```

**Complexité :**
- Temps : O(log n)
- Espace : O(1)

**Points clés :**
- Identification de la partie triée à chaque étape
- Vérification si target est dans la partie triée
- Gestion des cas limites (tableau de taille 1, pas de pivot)

</details>

## 🚀 Exercices similaires

- [Search in Rotated Sorted Array II](../hard/search-rotated-ii.md) - Avec doublons
- [Find Minimum in Rotated Sorted Array](../medium/find-minimum.md) - Trouver le pivot
- [Binary Search](../easy/binary-search.md) - Version de base

## 🎓 Concepts clés

- **Array pivoté** : une rotation preserve l'ordre local
- **Invariant de moitié triée** : au moins une moitié reste triée
- **Binary Search conditionnel** : adapter la logique selon la structure
- **Gestion de cas complexes** : plusieurs conditions à vérifier