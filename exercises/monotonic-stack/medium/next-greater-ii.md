# Exercice - Next Greater Element II

**Niveau :** Medium
**Pattern :** Monotonic Stack
**Retour au cours :** [Monotonic Stack Pattern](../../courses/04-stack-monotonic.md)

## 📝 Énoncé

Étant donné un tableau circulaire `nums` (le prochain élément du dernier élément est le premier élément du tableau), retournez le prochain élément plus grand pour chaque élément dans `nums`.

Le prochain élément plus grand d'un nombre `x` est le premier élément plus grand traversé dans le sens des aiguilles d'une montre, ce qui signifie que vous pourriez rechercher de manière circulaire pour trouver son prochain élément plus grand. S'il n'existe pas, retournez `-1` pour ce nombre.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums = [1,2,1]
Output: [2,-1,2]
Explication:
- Pour le premier 1 : le prochain élément plus grand est 2
- Pour le 2 : il n'y a pas d'élément plus grand → -1
- Pour le dernier 1 : en tournant, le prochain élément plus grand est 2
```

**Exemple 2 :**
```
Input: nums = [1,2,3,4,3]
Output: [2,3,4,-1,4]
Explication:
- 1 → 2 (position suivante)
- 2 → 3 (position suivante)
- 3 → 4 (position suivante)
- 4 → -1 (aucun élément plus grand)
- 3 → 4 (en revenant au début, trouve 4)
```

**Exemple 3 :**
```
Input: nums = [5,4,3,2,1]
Output: [-1,-1,-1,-1,-1]
Explication: Tableau décroissant, aucun élément n'a de "next greater"
```

## 🎯 Contraintes

- `1 <= nums.length <= 10^4`
- `-10^9 <= nums[i] <= 10^9`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Pour un tableau circulaire, vous pouvez "simuler" la circularité en parcourant le tableau deux fois : `for i := 0; i < 2*n; i++`.

</details>

<details>
<summary>Indice 2</summary>

Utilisez `i % n` pour obtenir l'indice réel dans le tableau original lors du double parcours.

</details>

<details>
<summary>Indice 3</summary>

Utilisez une monotonic stack décroissante qui stocke les indices. Lors de la seconde iteration, ne push que si `i < n`.

</details>

<details>
<summary>Indice 4</summary>

Structure : stack d'indices + double parcours + condition `i < n` pour éviter les doublons dans la stack.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func nextGreaterElements(nums []int) []int {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println(nextGreaterElements([]int{1, 2, 1}))
    // Expected: [2, -1, 2]

    fmt.Println(nextGreaterElements([]int{1, 2, 3, 4, 3}))
    // Expected: [2, 3, 4, -1, 4]

    fmt.Println(nextGreaterElements([]int{5, 4, 3, 2, 1}))
    // Expected: [-1, -1, -1, -1, -1]

    fmt.Println(nextGreaterElements([]int{1}))
    // Expected: [-1]

    fmt.Println(nextGreaterElements([]int{1, 1, 1, 1}))
    // Expected: [-1, -1, -1, -1]

    fmt.Println(nextGreaterElements([]int{2, 1, 2, 4, 3, 1}))
    // Expected: [4, 2, 4, -1, 4, 2]

    fmt.Println(nextGreaterElements([]int{100, 1, 11, 1, 120, 111, 123, 1, -1, -100}))
    // Expected: [120, 11, 120, 120, 123, 123, -1, 100, 100, 100]

    fmt.Println(nextGreaterElements([]int{3, 8, 4, 1, 2}))
    // Expected: [8, -1, 8, 2, 3]
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func nextGreaterElements(nums []int) []int {
    n := len(nums)
    result := make([]int, n)
    // Initialiser à -1 (cas par défaut)
    for i := range result {
        result[i] = -1
    }

    stack := []int{} // Stack d'indices (monotonic décroissante)

    // Parcourir le tableau deux fois pour simuler la circularité
    for i := 0; i < 2*n; i++ {
        actualIndex := i % n // Indice réel dans nums

        // Pop tous les indices avec valeur plus petite
        for len(stack) > 0 && nums[stack[len(stack)-1]] < nums[actualIndex] {
            prevIndex := stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            result[prevIndex] = nums[actualIndex]
        }

        // Push seulement lors de la première itération (i < n)
        // Évite les doublons dans la stack
        if i < n {
            stack = append(stack, actualIndex)
        }
    }

    return result
}
```

**Simulation détaillée pour `[1,2,1]` :**

```
n=3, résultat initial: [-1,-1,-1]

Première itération (i=0 à 2):
i=0, actualIndex=0, nums[0]=1: stack=[] → push 0 → stack=[0]
i=1, actualIndex=1, nums[1]=2: stack=[0], nums[0]=1<2
    → pop 0, result[0]=2 → push 1 → stack=[1]
i=2, actualIndex=2, nums[2]=1: stack=[1], nums[1]=2>1 → push 2 → stack=[1,2]

Après première itération: result=[2,-1,-1], stack=[1,2]

Deuxième itération (i=3 à 5):
i=3, actualIndex=0, nums[0]=1: stack=[1,2], nums[2]=1≥1 → pas de pop
i=4, actualIndex=1, nums[1]=2: stack=[1,2], nums[2]=1<2
    → pop 2, result[2]=2 → stack=[1]
i=5, actualIndex=2, nums[2]=1: stack=[1], nums[1]=2>1 → pas de pop

Résultat final: [2,-1,2]
```

**Simulation détaillée pour `[1,2,3,4,3]` :**

```
n=5, résultat initial: [-1,-1,-1,-1,-1]

Première itération:
i=0: nums[0]=1 → push 0 → stack=[0]
i=1: nums[1]=2 > nums[0]=1 → pop 0, result[0]=2 → push 1 → stack=[1]
i=2: nums[2]=3 > nums[1]=2 → pop 1, result[1]=3 → push 2 → stack=[2]
i=3: nums[3]=4 > nums[2]=3 → pop 2, result[2]=4 → push 3 → stack=[3]
i=4: nums[4]=3 < nums[3]=4 → push 4 → stack=[3,4]

Après première: result=[2,3,4,-1,-1], stack=[3,4]

Deuxième itération:
i=5: actualIndex=0, nums[0]=1 < nums[4]=3 → pas de changement
i=6: actualIndex=1, nums[1]=2 < nums[4]=3 → pas de changement
i=7: actualIndex=2, nums[2]=3 = nums[4]=3 → pas de changement
i=8: actualIndex=3, nums[3]=4 > nums[4]=3 → pop 4, result[4]=4 → stack=[3]
i=9: actualIndex=4, nums[4]=3 < nums[3]=4 → pas de changement

Résultat final: [2,3,4,-1,4]
```

**Explication de l'algorithme :**

1. **Double parcours** : simule la circularité en parcourant 2×n éléments
2. **Modulo pour l'indice** : `i % n` donne l'indice réel dans le tableau
3. **Stack d'indices** : maintient les positions des éléments en attente
4. **Condition push** : `i < n` évite de dupliquer les éléments dans la stack
5. **Monotonic décroissante** : pop quand `nums[stack_top] < nums[current]`

**Complexité :**
- Temps : O(n) - chaque élément est pushé et poppé au plus une fois
- Espace : O(n) - stack et tableau résultat

**Pourquoi ça marche :**
- Le double parcours garantit que chaque élément "voit" tous les éléments suivants dans un contexte circulaire
- La condition `i < n` pour push évite les éléments dupliqués dans la stack
- La stack maintient l'ordre des éléments non résolus

</details>

## 🎯 Points clés à retenir

1. **Circularité par double parcours** : parcourir 2×n éléments avec modulo
2. **Condition de push** : `i < n` pour éviter les doublons dans la stack
3. **Stack d'indices** : permet de calculer les positions et accéder aux valeurs
4. **Même pattern** : monotonic stack décroissante comme Next Greater I

## 🚀 Exercices similaires

- [Next Greater Element I](../easy/next-greater-i.md) - Version non-circulaire
- [Daily Temperatures](./daily-temperatures.md) - Pattern similar avec indices
- [Largest Rectangle in Histogram](./largest-rectangle.md) - Autre usage avancé

## 🔍 Variations

**Version Previous Greater Circular :**
```go
// Pour trouver le previous greater element dans un tableau circulaire
for i := 2*n - 1; i >= 0; i-- {
    actualIndex := i % n
    // Même logique mais parcours inversé
}
```

**Version avec limite de recherche :**
```go
// Limiter la recherche circulaire à k éléments
for i := 0; i < min(2*n, n+k); i++ {
    // même logique
}
```