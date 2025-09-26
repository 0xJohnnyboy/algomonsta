# Exercice - Find Peak Element

**Niveau :** Medium
**Pattern :** Binary Search for Peak
**Retour au cours :** [Binary Search Pattern](../../courses/03-binary-search.md)

## 📝 Énoncé

Un élément de pic est un élément qui est strictement supérieur à ses voisins.

Étant donné un tableau d'entiers `nums` indexé à partir de 0, trouvez un élément de pic et retournez son index. S'il y a plusieurs éléments de pic, retournez l'index de **n'importe lequel**.

Vous pouvez imaginer que `nums[-1] = nums[n] = -∞`. En d'autres termes, un élément est toujours considéré comme strictement plus petit qu'un élément de pic.

Vous devez écrire un algorithme qui s'exécute en temps `O(log n)`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [1,2,3,1]
Output: 2
Explication: 3 est un élément de pic et votre fonction devrait retourner l'index 2
```

**Exemple 2 :**
```
Input: nums = [1,2,1,3,5,6,4]
Output: 5
Explication: Votre fonction peut retourner l'index 1 (où le pic est 2)
             ou l'index 5 (où le pic est 6)
```

**Exemple 3 :**
```
Input: nums = [1]
Output: 0
Explication: Le seul élément est un pic par définition
```

## 🎯 Contraintes

- `1 <= nums.length <= 1000`
- `-2^31 <= nums[i] <= 2^31 - 1`
- `nums[i] != nums[i + 1]` pour tous les `i` valides

## 💡 Indices

<details>
<summary>Indice 1</summary>

Puisque `nums[-1] = nums[n] = -∞`, il y a toujours au moins un pic dans le tableau.

</details>

<details>
<summary>Indice 2</summary>

Si `nums[mid] < nums[mid+1]`, alors il y a forcément un pic dans la moitié droite.

</details>

<details>
<summary>Indice 3</summary>

Si `nums[mid] > nums[mid+1]`, alors il y a forcément un pic dans la moitié gauche (mid inclus).

</details>

<details>
<summary>Indice 4</summary>

Utilisez cette propriété pour éliminer une moitié du tableau à chaque étape de binary search.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func findPeakElement(nums []int) int {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println(findPeakElement([]int{1, 2, 3, 1}))           // Expected: 2
    fmt.Println(findPeakElement([]int{1, 2, 1, 3, 5, 6, 4})) // Expected: 1 ou 5
    fmt.Println(findPeakElement([]int{1}))                    // Expected: 0
    fmt.Println(findPeakElement([]int{1, 2}))                 // Expected: 1
    fmt.Println(findPeakElement([]int{2, 1}))                 // Expected: 0
    fmt.Println(findPeakElement([]int{1, 2, 3, 4, 5}))       // Expected: 4
    fmt.Println(findPeakElement([]int{5, 4, 3, 2, 1}))       // Expected: 0
    fmt.Println(findPeakElement([]int{1, 3, 2, 4, 6, 5}))    // Expected: 1, 3 ou 4
    fmt.Println(findPeakElement([]int{6, 5, 4, 3, 2, 3, 2})) // Expected: 0 ou 5
    fmt.Println(findPeakElement([]int{3, 4, 3, 2, 1}))       // Expected: 1
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func findPeakElement(nums []int) int {
    left, right := 0, len(nums)-1

    for left < right {
        mid := left + (right-left)/2

        if nums[mid] < nums[mid+1] {
            // La pente monte vers la droite,
            // il y a forcément un pic à droite
            left = mid + 1
        } else {
            // nums[mid] > nums[mid+1]
            // La pente descend vers la droite,
            // il y a forcément un pic à gauche (mid inclus)
            right = mid
        }
    }

    return left
}
```

**Explication détaillée :**

1. **Propriété clé :** Avec `nums[-1] = nums[n] = -∞`, il y a toujours au moins un pic

2. **Logique de binary search :**

   **Cas 1 : `nums[mid] < nums[mid+1]`**
   - La pente monte vers la droite
   - Il y a forcément un pic dans `[mid+1, right]`
   - `left = mid + 1`

   **Cas 2 : `nums[mid] > nums[mid+1]`**
   - La pente descend vers la droite
   - `mid` pourrait être un pic, ou il y en a un dans `[left, mid]`
   - `right = mid` (on garde mid)

3. **Convergence :** Quand `left == right`, on a trouvé un pic

**Pourquoi ça marche ?**

- Si on va vers une pente montante, on finira par atteindre un sommet
- Si on va vers une pente descendante, on trouvera un sommet avant ou au point actuel
- Les "bords" `nums[-1]` et `nums[n]` étant `-∞`, garantissent l'existence d'un pic

**Exemple de trace pour `nums = [1,2,3,1]` :**

```
Début: left=0, right=3
mid=1, nums[1]=2 < nums[2]=3 → left=2
mid=2, nums[2]=3 > nums[3]=1 → right=2
left==right=2 → retourner 2
```

**Complexité :**
- Temps : O(log n)
- Espace : O(1)

**Points clés :**
- Exploitation de la propriété des "bords infinis"
- Choix de direction basé sur la pente locale
- Template "find any" avec `left < right`

</details>

## 🚀 Exercices similaires

- [Find Peak Element II](../hard/find-peak-2d.md) - Version 2D
- [Find Minimum in Rotated Sorted Array](../medium/find-minimum.md) - Autre recherche de propriété
- [Search in Rotated Sorted Array](search-rotated.md) - Binary search modifié

## 🎓 Concepts clés

- **Peak finding** : recherche d'optimum local
- **Propriété de pente** : utiliser la direction pour guider la recherche
- **Bords virtuels** : technique pour garantir l'existence d'une solution
- **Binary search adaptatif** : choisir la direction selon les données locales