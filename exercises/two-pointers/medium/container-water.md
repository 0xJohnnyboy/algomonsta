# Exercice - Container With Most Water

**Niveau :** Medium
**Pattern :** Two Pointers
**Retour :** [📚 Cours Two Pointers](../../courses/01-two-pointers.md)

## 📝 Énoncé

Vous avez un tableau d'entiers `height` de longueur `n`. Il y a `n` lignes verticales dessinées de sorte que les deux extrémités de la `i`ème ligne sont `(i, 0)` et `(i, height[i])`.

Trouvez deux lignes qui, avec l'axe des x, forment un conteneur qui contient le plus d'eau.

Retournez la quantité maximale d'eau qu'un conteneur peut stocker.

**Remarque :** Vous ne pouvez pas incliner le conteneur.

## 🔍 Exemples

**Exemple 1 :**
```
Input: height = [1,8,6,2,5,4,8,3,7]
Output: 49
Explication: Les lignes verticales ci-dessus sont représentées par le tableau [1,8,6,2,5,4,8,3,7].
Dans ce cas, la quantité maximale d'eau (zone bleue) que le conteneur peut contenir est 49.
```

**Exemple 2 :**
```
Input: height = [1,1]
Output: 1
```

## 🎯 Contraintes

- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

## 💡 Indices

<details>
<summary>Indice 1</summary>

La surface d'eau est déterminée par la distance entre les lignes et la hauteur de la ligne la plus courte.

</details>

<details>
<summary>Indice 2</summary>

Utilisez deux pointeurs aux extrémités du tableau. La ligne la plus courte limite la capacité.

</details>

<details>
<summary>Indice 3</summary>

Déplacez le pointeur de la ligne la plus courte vers l'intérieur pour potentiellement trouver une meilleure solution.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func maxArea(height []int) int {
    // TODO: Implémentez votre solution ici
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}

func main() {
    // Tests fournis
    tests := []struct {
        input    []int
        expected int
    }{
        {[]int{1, 8, 6, 2, 5, 4, 8, 3, 7}, 49},
        {[]int{1, 1}, 1},
        {[]int{1, 2, 1}, 2},
        {[]int{1, 2, 4, 3}, 4},
        {[]int{2, 3, 4, 5, 18, 17, 6}, 17},
        {[]int{1, 3, 2, 5, 25, 24, 5}, 24},
    }

    fmt.Println("=== Tests Container With Most Water ===")
    for i, test := range tests {
        result := maxArea(test.input)
        status := "✅"
        if result != test.expected {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: %v\n", test.input)
        fmt.Printf("  Expected: %d, Got: %d\n\n", test.expected, result)
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func maxArea(height []int) int {
    left := 0
    right := len(height) - 1
    maxWater := 0

    for left < right {
        // Calculer la surface avec les lignes actuelles
        width := right - left
        currentArea := width * min(height[left], height[right])
        maxWater = max(maxWater, currentArea)

        // Déplacer le pointeur de la ligne la plus courte
        if height[left] < height[right] {
            left++
        } else {
            right--
        }
    }

    return maxWater
}
```

**Explication :**
1. Initialisez deux pointeurs aux extrémités du tableau
2. Calculez la surface avec la largeur et la hauteur minimale
3. Déplacez le pointeur de la ligne la plus courte (car bouger la plus haute ne peut qu'empirer)
4. Continuez jusqu'à ce que les pointeurs se rencontrent

**Complexité :**
- Temps : O(n) - un seul passage à travers le tableau
- Espace : O(1) - seuls quelques variables utilisées

</details>

## 🚀 Exercices similaires

- [Three Sum](three-sum.md)
- [Trapping Rain Water](../hard/trapping-rain-water.md)
- [Sort Colors](sort-colors.md)