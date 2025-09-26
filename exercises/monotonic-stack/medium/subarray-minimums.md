# Exercice - Sum of Subarray Minimums

**Niveau :** Medium
**Pattern :** Monotonic Stack
**Retour au cours :** [Monotonic Stack Pattern](../../courses/04-stack-monotonic.md)

## 📝 Énoncé

Étant donné un tableau d'entiers `arr`, trouvez la somme des éléments minimum de tous les sous-tableaux possibles de `arr`.

Puisque la réponse peut être très grande, retournez-la **modulo** `10^9 + 7`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: arr = [3,1,2,4]
Output: 17
Explication:
Sous-tableaux et leurs minimums:
[3] → min = 3
[1] → min = 1
[2] → min = 2
[4] → min = 4
[3,1] → min = 1
[1,2] → min = 1
[2,4] → min = 2
[3,1,2] → min = 1
[1,2,4] → min = 1
[3,1,2,4] → min = 1
Somme: 3+1+2+4+1+1+2+1+1+1 = 17
```

**Exemple 2 :**
```
Input: arr = [11,81,94,43,3]
Output: 444
```

**Exemple 3 :**
```
Input: arr = [2,9,7,8,3,4,6,1]
Output: 187
```

## 🎯 Contraintes

- `1 <= arr.length <= 3 * 10^4`
- `1 <= arr[i] <= 3 * 10^4`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Pour chaque élément, calculez dans combien de sous-tableaux il apparaît comme minimum. Cela nécessite de trouver sa "portée" à gauche et à droite.

</details>

<details>
<summary>Indice 2</summary>

Utilisez deux passes avec monotonic stack : une pour trouver le "previous smaller element" et une pour le "next smaller element".

</details>

<details>
<summary>Indice 3</summary>

Si un élément `arr[i]` est le minimum dans une plage, il contribue `arr[i] × (nombre de sous-tableaux dans cette plage)` à la somme totale.

</details>

<details>
<summary>Indice 4</summary>

Contribution d'un élément à l'indice `i` : `arr[i] × (i - left + 1) × (right - i + 1)` où `left` et `right` sont les bornes de sa dominance.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func sumSubarrayMins(arr []int) int {
    const MOD = 1000000007
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println(sumSubarrayMins([]int{3, 1, 2, 4}))
    // Expected: 17

    fmt.Println(sumSubarrayMins([]int{11, 81, 94, 43, 3}))
    // Expected: 444

    fmt.Println(sumSubarrayMins([]int{2, 9, 7, 8, 3, 4, 6, 1}))
    // Expected: 187

    fmt.Println(sumSubarrayMins([]int{1}))
    // Expected: 1

    fmt.Println(sumSubarrayMins([]int{1, 2, 3}))
    // Expected: 10

    fmt.Println(sumSubarrayMins([]int{3, 2, 1}))
    // Expected: 10

    fmt.Println(sumSubarrayMins([]int{5, 5, 5}))
    // Expected: 30

    fmt.Println(sumSubarrayMins([]int{1, 7, 5, 2, 4, 3, 9}))
    // Expected: 84
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func sumSubarrayMins(arr []int) int {
    const MOD = 1000000007
    n := len(arr)

    // Tableaux pour stocker les distances aux éléments plus petits
    left := make([]int, n)   // Distance au previous smaller element
    right := make([]int, n)  // Distance au next smaller element

    // Calculer les distances à gauche (previous smaller)
    stack := []int{}
    for i := 0; i < n; i++ {
        // Pop tous les éléments >= arr[i]
        for len(stack) > 0 && arr[stack[len(stack)-1]] >= arr[i] {
            stack = stack[:len(stack)-1]
        }

        if len(stack) == 0 {
            left[i] = i + 1  // Distance jusqu'au début
        } else {
            left[i] = i - stack[len(stack)-1]  // Distance à l'élément plus petit
        }

        stack = append(stack, i)
    }

    // Calculer les distances à droite (next smaller)
    stack = []int{}  // Reset stack
    for i := n - 1; i >= 0; i-- {
        // Pop tous les éléments > arr[i] (strictement supérieur pour éviter doublons)
        for len(stack) > 0 && arr[stack[len(stack)-1]] > arr[i] {
            stack = stack[:len(stack)-1]
        }

        if len(stack) == 0 {
            right[i] = n - i  // Distance jusqu'à la fin
        } else {
            right[i] = stack[len(stack)-1] - i  // Distance à l'élément plus petit
        }

        stack = append(stack, i)
    }

    // Calculer la somme totale
    result := 0
    for i := 0; i < n; i++ {
        contribution := (arr[i] * left[i] % MOD) * right[i] % MOD
        result = (result + contribution) % MOD
    }

    return result
}
```

**Simulation détaillée pour `[3,1,2,4]` :**

```
Étape 1 - Calcul des distances à gauche (previous smaller):
i=0, arr[0]=3: stack=[] → left[0]=0-(-1)=1, stack=[0]
i=1, arr[1]=1: stack=[0], arr[0]=3≥1 → pop 0
             stack=[] → left[1]=1-(-1)=2, stack=[1]
i=2, arr[2]=2: stack=[1], arr[1]=1<2 → left[2]=2-1=1, stack=[1,2]
i=3, arr[3]=4: stack=[1,2], arr[2]=2<4 → left[3]=3-2=1, stack=[1,2,3]

left = [1,2,1,1]

Étape 2 - Calcul des distances à droite (next smaller):
i=3, arr[3]=4: stack=[] → right[3]=4-3=1, stack=[3]
i=2, arr[2]=2: stack=[3], arr[3]=4>2 → pop 3
             stack=[] → right[2]=4-2=2, stack=[2]
i=1, arr[1]=1: stack=[2], arr[2]=2>1 → pop 2
             stack=[] → right[1]=4-1=3, stack=[1]
i=0, arr[0]=3: stack=[1], arr[1]=1<3 → right[0]=1-0=1, stack=[1,0]

right = [1,3,2,1]

Étape 3 - Calcul des contributions:
i=0: contribution = 3 × 1 × 1 = 3
i=1: contribution = 1 × 2 × 3 = 6
i=2: contribution = 2 × 1 × 2 = 4
i=3: contribution = 4 × 1 × 1 = 4

Somme totale: 3 + 6 + 4 + 4 = 17
```

**Explication visuelle pour l'élément à l'indice 1 (valeur=1) :**
```
arr = [3,1,2,4]
       0 1 2 3

L'élément 1 à l'indice 1 est minimum dans ces sous-tableaux:
[1]     → indice 1 à 1
[3,1]   → indice 0 à 1
[1,2]   → indice 1 à 2
[3,1,2] → indice 0 à 2
[1,2,4] → indice 1 à 3
[3,1,2,4] → indice 0 à 3

Nombre de sous-tableaux = left[1] × right[1] = 2 × 3 = 6
Contribution = 1 × 6 = 6
```

**Explication de l'algorithme :**

1. **Previous Smaller** : trouve la distance à l'élément précédent plus petit
2. **Next Smaller** : trouve la distance à l'élément suivant plus petit (strictement)
3. **Contribution** : `arr[i] × left[i] × right[i]` = valeur × nombre de sous-tableaux où c'est le min
4. **Gestion des doublons** : utilise `>=` vs `>` pour éviter de compter deux fois les éléments égaux

**Complexité :**
- Temps : O(n) - deux passes avec monotonic stack
- Espace : O(n) - stack et tableaux auxiliaires

**Pourquoi ça marche :**
- Chaque élément est minimum dans exactement `left[i] × right[i]` sous-tableaux
- La contribution totale est la somme de toutes ces contributions individuelles
- Les distances calculées définissent précisément la "zone de dominance" de chaque élément

</details>

## 🎯 Points clés à retenir

1. **Contribution individuelle** : chaque élément contribue dans un nombre calculable de sous-tableaux
2. **Deux monotonic stacks** : une pour previous smaller, une pour next smaller
3. **Gestion des doublons** : différence entre `>=` et `>` dans les conditions de pop
4. **Modulo arithmétique** : appliquer MOD à chaque étape pour éviter l'overflow

## 🚀 Exercices similaires

- [Largest Rectangle in Histogram](./largest-rectangle.md) - Même concept de "zone de dominance"
- [Maximal Rectangle](../hard/largest-rectangle-matrix.md) - Extension 2D
- [Daily Temperatures](./daily-temperatures.md) - Pattern monotonic stack basique

## 🔍 Variations

**Version Sum of Subarray Maximums :**
```go
// Remplacer les conditions pour chercher larger elements
for len(stack) > 0 && arr[stack[len(stack)-1]] <= arr[i] {
    // Previous larger
}

for len(stack) > 0 && arr[stack[len(stack)-1]] < arr[i] {
    // Next larger
}
```

**Version avec range queries :**
```go
// Pour calculer la somme des minimums dans une plage [left, right]
func sumSubarrayMinsRange(arr []int, left, right int) int {
    // Même algorithme mais limité à la plage
}
```

**Version optimisée en une passe :**
```go
func sumSubarrayMinsOnePass(arr []int) int {
    stack := []int{}
    result := 0

    for i := 0; i <= len(arr); i++ {
        currentVal := 0
        if i < len(arr) {
            currentVal = arr[i]
        }

        for len(stack) > 0 && arr[stack[len(stack)-1]] >= currentVal {
            // Calculer contribution directement
        }
    }

    return result
}
```