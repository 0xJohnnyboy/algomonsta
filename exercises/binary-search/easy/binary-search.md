# Exercice - Binary Search

**Niveau :** Easy
**Pattern :** Classic Binary Search
**Retour au cours :** [Binary Search Pattern](../../courses/03-binary-search.md)

## 📝 Énoncé

Étant donné un tableau d'entiers `nums` trié par **ordre croissant**, et un entier `target`, écrivez une fonction pour rechercher `target` dans `nums`. Si `target` existe, retournez son index. Sinon, retournez `-1`.

Vous devez écrire un algorithme avec une complexité temporelle de `O(log n)`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [-1,0,3,5,9,12], target = 9
Output: 4
Explication: 9 existe dans nums et son index est 4
```

**Exemple 2 :**
```
Input: nums = [-1,0,3,5,9,12], target = 2
Output: -1
Explication: 2 n'existe pas dans nums donc on retourne -1
```

**Exemple 3 :**
```
Input: nums = [5], target = 5
Output: 0
```

## 🎯 Contraintes

- `1 <= nums.length <= 10^4`
- `-10^4 < nums[i], target < 10^4`
- Tous les éléments de `nums` sont **uniques**
- `nums` est trié par ordre croissant

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est l'implémentation classique de la recherche binaire. Divisez l'espace de recherche en deux à chaque itération.

</details>

<details>
<summary>Indice 2</summary>

Utilisez deux pointeurs `left` et `right` pour définir les limites de votre espace de recherche.

</details>

<details>
<summary>Indice 3</summary>

À chaque étape, calculez `mid = left + (right - left) / 2` pour éviter les débordements d'entiers.

</details>

<details>
<summary>Indice 4</summary>

Si `nums[mid] == target`, vous avez trouvé ! Si `nums[mid] < target`, cherchez à droite. Sinon, cherchez à gauche.

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
    fmt.Println(search([]int{-1, 0, 3, 5, 9, 12}, 9))   // Expected: 4
    fmt.Println(search([]int{-1, 0, 3, 5, 9, 12}, 2))   // Expected: -1
    fmt.Println(search([]int{5}, 5))                      // Expected: 0
    fmt.Println(search([]int{1, 3, 5, 7, 9}, 3))        // Expected: 1
    fmt.Println(search([]int{1, 3, 5, 7, 9}, 6))        // Expected: -1
    fmt.Println(search([]int{2, 4, 6, 8, 10, 12}, 10))  // Expected: 4
    fmt.Println(search([]int{1, 2, 3, 4, 5}, 1))        // Expected: 0
    fmt.Println(search([]int{1, 2, 3, 4, 5}, 5))        // Expected: 4
    fmt.Println(search([]int{10}, 10))                   // Expected: 0
    fmt.Println(search([]int{1, 3}, 2))                  // Expected: -1
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
        } else if nums[mid] < target {
            // target est dans la moitié droite
            left = mid + 1
        } else {
            // target est dans la moitié gauche
            right = mid - 1
        }
    }

    // target non trouvé
    return -1
}
```

**Explication détaillée :**

1. **Initialisation :** `left = 0`, `right = len(nums) - 1`
   - Définit l'espace de recherche initial

2. **Boucle de recherche :** Continue tant que `left <= right`
   - L'espace de recherche contient au moins un élément

3. **Calcul du milieu :** `mid = left + (right - left) / 2`
   - Évite les débordements d'entiers comparé à `(left + right) / 2`

4. **Comparaison avec target :**
   - Si `nums[mid] == target` → trouvé, retourner `mid`
   - Si `nums[mid] < target` → chercher dans la moitié droite
   - Si `nums[mid] > target` → chercher dans la moitié gauche

5. **Sortie de boucle :** Si `left > right`, l'élément n'existe pas

**Complexité :**
- Temps : O(log n) - divise l'espace de recherche par 2 à chaque itération
- Espace : O(1) - utilise seulement quelques variables

**Points clés :**
- Template classique de binary search
- Attention à la condition de boucle `left <= right`
- Utilisation de `left + (right - left) / 2` pour éviter les débordements

</details>

## 🚀 Exercices similaires

- [Search Insert Position](search-insert.md) - Variante pour trouver la position d'insertion
- [First Bad Version](first-bad-version.md) - Binary search avec API
- [Search in Rotated Sorted Array](../medium/search-rotated.md) - Version plus complexe

## 🎓 Concepts clés

- **Binary Search classique** : diviser pour régner
- **Invariants de boucle** : maintenir les propriétés de l'espace de recherche
- **Éviter les débordements** : calcul sécurisé du milieu
- **Complexité logarithmique** : réduction de moitié de l'espace de recherche