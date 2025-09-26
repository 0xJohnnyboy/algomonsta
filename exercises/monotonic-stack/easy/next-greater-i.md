# Exercice - Next Greater Element I

**Niveau :** Easy
**Pattern :** Monotonic Stack
**Retour au cours :** [Monotonic Stack Pattern](../../courses/04-stack-monotonic.md)

## 📝 Énoncé

Vous disposez de deux tableaux d'entiers **distincts** `nums1` et `nums2`, où `nums1` est un sous-ensemble de `nums2`.

Trouvez tous les prochains éléments plus grands dans `nums2` correspondant à chaque élément de `nums1`.

Le prochain élément plus grand d'un nombre `x` dans `nums1` est le premier élément plus grand à sa droite dans `nums2`. S'il n'existe pas, la réponse pour ce nombre est `-1`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: nums1 = [4,1,2], nums2 = [1,3,4,2]
Output: [-1,3,-1]
Explication:
- Le prochain élément plus grand pour 4 est -1 (n'existe pas)
- Le prochain élément plus grand pour 1 est 3 (position suivante dans nums2)
- Le prochain élément plus grand pour 2 est -1 (n'existe pas)
```

**Exemple 2 :**
```
Input: nums1 = [2,4], nums2 = [1,2,3,4]
Output: [3,-1]
Explication:
- Le prochain élément plus grand pour 2 est 3
- Le prochain élément plus grand pour 4 est -1 (n'existe pas)
```

**Exemple 3 :**
```
Input: nums1 = [1,3,5,2,4], nums2 = [6,5,4,3,2,1,7]
Output: [7,7,7,7,7]
```

## 🎯 Contraintes

- `1 <= nums1.length <= nums2.length <= 1000`
- `0 <= nums1[i], nums2[i] <= 10^4`
- Tous les entiers dans `nums1` et `nums2` sont uniques
- Tous les entiers de `nums1` apparaissent aussi dans `nums2`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez une monotonic stack pour parcourir `nums2` et construire une map des "next greater elements".

</details>

<details>
<summary>Indice 2</summary>

Parcourez `nums2` avec une stack décroissante. Quand vous trouvez un élément plus grand, il devient le "next greater" de tous les éléments plus petits dans la stack.

</details>

<details>
<summary>Indice 3</summary>

Stockez le résultat dans une map `nextGreater[element] = nextGreaterElement`, puis consultez cette map pour chaque élément de `nums1`.

</details>

<details>
<summary>Indice 4</summary>

Algorithme en 2 étapes : 1) Construire la map avec monotonic stack sur `nums2`, 2) Mapper les résultats pour `nums1`.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func nextGreaterElement(nums1 []int, nums2 []int) []int {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println(nextGreaterElement([]int{4, 1, 2}, []int{1, 3, 4, 2}))
    // Expected: [-1, 3, -1]

    fmt.Println(nextGreaterElement([]int{2, 4}, []int{1, 2, 3, 4}))
    // Expected: [3, -1]

    fmt.Println(nextGreaterElement([]int{1, 3, 5, 2, 4}, []int{6, 5, 4, 3, 2, 1, 7}))
    // Expected: [7, 7, 7, 7, 7]

    fmt.Println(nextGreaterElement([]int{1}, []int{1, 2}))
    // Expected: [2]

    fmt.Println(nextGreaterElement([]int{3, 1}, []int{1, 3, 2}))
    // Expected: [-1, 3]

    fmt.Println(nextGreaterElement([]int{1, 2}, []int{2, 1, 3}))
    // Expected: [3, 3]

    fmt.Println(nextGreaterElement([]int{5}, []int{1, 2, 3, 5}))
    // Expected: [-1]

    fmt.Println(nextGreaterElement([]int{1, 2, 3}, []int{3, 2, 1}))
    // Expected: [-1, -1, -1]
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func nextGreaterElement(nums1 []int, nums2 []int) []int {
    // Map pour stocker le next greater element de chaque nombre
    nextGreater := make(map[int]int)
    stack := []int{} // Stack monotonic décroissante

    // Étape 1: Construire la map avec monotonic stack sur nums2
    for _, num := range nums2 {
        // Pop tous les éléments plus petits que num
        for len(stack) > 0 && stack[len(stack)-1] < num {
            smaller := stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            nextGreater[smaller] = num // num est le next greater de smaller
        }
        // Push l'élément actuel
        stack = append(stack, num)
    }

    // Étape 2: Construire le résultat pour nums1
    result := make([]int, len(nums1))
    for i, num := range nums1 {
        if val, exists := nextGreater[num]; exists {
            result[i] = val
        } else {
            result[i] = -1
        }
    }

    return result
}
```

**Simulation détaillée pour `nums1=[4,1,2]`, `nums2=[1,3,4,2]` :**

```
Étape 1 - Construction de la map:
num=1: stack=[] → push 1 → stack=[1]
num=3: stack=[1], 1<3 → pop 1, nextGreater[1]=3 → stack=[3]
num=4: stack=[3], 3<4 → pop 3, nextGreater[3]=4 → stack=[4]
num=2: stack=[4], 4>2 → push 2 → stack=[4,2]

Map finale: nextGreater = {1:3, 3:4}

Étape 2 - Construction du résultat:
nums1[0]=4: nextGreater[4] n'existe pas → result[0]=-1
nums1[1]=1: nextGreater[1]=3 → result[1]=3
nums1[2]=2: nextGreater[2] n'existe pas → result[2]=-1

Résultat final: [-1,3,-1]
```

**Explication de l'algorithme :**

1. **Monotonic Stack Pattern** : utilise une stack décroissante sur `nums2`
2. **Map de résultats** : stocke `nextGreater[element] = nextGreaterElement`
3. **Condition de pop** : `stack_top < current_element`
4. **Lookup final** : consulte la map pour chaque élément de `nums1`

**Complexité :**
- Temps : O(m + n) où m = len(nums1), n = len(nums2)
- Espace : O(n) pour la stack et la map

**Pourquoi ça marche :**
- La stack maintient les éléments en attente d'un "next greater"
- Quand on trouve un élément plus grand, il "résout" tous les plus petits dans la stack
- La map permet une lookup O(1) pour construire le résultat final

</details>

## 🎯 Points clés à retenir

1. **Deux étapes** : construire la map avec monotonic stack, puis mapper les résultats
2. **Stack décroissante** : maintient les éléments en attente d'un next greater
3. **Map pour lookup** : permet une recherche efficace des résultats
4. **Complexité optimale** : O(m + n) au lieu de O(m × n) avec force brute

## 🚀 Exercices similaires

- [Daily Temperatures](../medium/daily-temperatures.md) - Problème similaire avec indices
- [Next Greater Element II](../medium/next-greater-ii.md) - Version avec tableau circulaire
- [Baseball Game](./baseball-game.md) - Autre application des stacks