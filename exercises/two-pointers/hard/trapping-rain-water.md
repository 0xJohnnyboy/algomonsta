# Exercice - Trapping Rain Water

**Niveau :** Hard
**Pattern :** Two Pointers
**Retour :** [📚 Cours Two Pointers](../../courses/01-two-pointers.md)

## 📝 Énoncé

Étant donné `n` entiers non négatifs représentant une carte d'élévation où la largeur de chaque barre est `1`, calculez la quantité d'eau qu'il est possible de piéger après qu'il ait plu.

## 🔍 Exemples

**Exemple 1 :**
```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explication: La carte d'élévation ci-dessus (section noire) est représentée par le tableau [0,1,0,2,1,0,1,3,2,1,2,1].
Dans ce cas, 6 unités d'eau de pluie (section bleue) sont piégées.
```

**Exemple 2 :**
```
Input: height = [4,2,0,3,2,5]
Output: 9
```

## 🎯 Contraintes

- `n == height.length`
- `1 <= n <= 2 * 10^4`
- `0 <= height[i] <= 3 * 10^4`

## 💡 Indices

<details>
<summary>Indice 1</summary>

L'eau piégée à chaque position dépend de la hauteur minimale entre le mur le plus haut à gauche et le mur le plus haut à droite.

</details>

<details>
<summary>Indice 2</summary>

Utilisez deux pointeurs en partant des extrémités. L'eau piégée est limitée par le côté avec la hauteur maximale la plus faible.

</details>

<details>
<summary>Indice 3</summary>

Maintenez les hauteurs maximales vues jusqu'à présent des deux côtés et calculez l'eau piégée en conséquence.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func trap(height []int) int {
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
        {[]int{0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1}, 6},
        {[]int{4, 2, 0, 3, 2, 5}, 9},
        {[]int{3, 0, 2, 0, 4}, 7},
        {[]int{0, 1, 0}, 0},
        {[]int{1}, 0},
        {[]int{1, 0, 1}, 1},
        {[]int{5, 4, 1, 2}, 1},
        {[]int{2, 1, 2}, 1},
        {[]int{3, 2, 0, 4}, 7},
    }

    fmt.Println("=== Tests Trapping Rain Water ===")
    for i, test := range tests {
        result := trap(test.input)
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
func trap(height []int) int {
    if len(height) < 3 {
        return 0
    }

    left := 0
    right := len(height) - 1
    leftMax := 0
    rightMax := 0
    water := 0

    for left < right {
        if height[left] < height[right] {
            // Le côté gauche est plus bas, traitons-le
            if height[left] >= leftMax {
                leftMax = height[left]
            } else {
                // L'eau peut être piégée
                water += leftMax - height[left]
            }
            left++
        } else {
            // Le côté droit est plus bas ou égal, traitons-le
            if height[right] >= rightMax {
                rightMax = height[right]
            } else {
                // L'eau peut être piégée
                water += rightMax - height[right]
            }
            right--
        }
    }

    return water
}
```

**Explication :**
1. Utilisez deux pointeurs aux extrémités du tableau
2. Maintenez les hauteurs maximales vues de chaque côté (`leftMax`, `rightMax`)
3. Traitez toujours le côté avec la hauteur actuelle plus faible
4. Si la hauteur actuelle est inférieure au maximum de son côté, l'eau peut être piégée
5. La quantité d'eau piégée est la différence entre le maximum et la hauteur actuelle

**Pourquoi ça marche :**
- L'eau piégée à une position est déterminée par `min(leftMax, rightMax) - height[i]`
- En traitant toujours le côté le plus bas, on garantit que le `rightMax` (ou `leftMax`) de l'autre côté est au moins aussi haut
- Donc on peut calculer l'eau piégée en utilisant seulement le maximum du côté traité

**Complexité :**
- Temps : O(n) - un seul passage à travers le tableau
- Espace : O(1) - seules quelques variables utilisées

</details>

## 🚀 Exercices similaires

- [Container With Most Water](../medium/container-water.md)
- [Three Sum](../medium/three-sum.md)
- [Largest Rectangle in Histogram](../../monotonic-stack/medium/largest-rectangle.md)