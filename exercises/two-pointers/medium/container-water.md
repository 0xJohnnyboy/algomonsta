# Exercice - Container With Most Water

**Niveau :** Medium
**Pattern :** Two Pointers
**Retour :** [📚 Cours Two Pointers](../../courses/01-two-pointers.md)

## 📝 Énoncé

Imaginez que vous avez des **murs verticaux** de différentes hauteurs. Vous voulez choisir **deux murs** qui peuvent contenir le plus d'eau entre eux.

Vous avez un tableau d'entiers `height` où `height[i]` représente la hauteur du mur à la position `i`.

**🧠 Principe :** La quantité d'eau = **largeur × hauteur**
- **Largeur** = distance entre les deux murs choisis
- **Hauteur** = le plus petit des deux murs (l'eau déborde du mur le plus court)

**🎯 Objectif :** Trouvez les deux murs qui donnent la **surface d'eau maximale**.

**Remarque :** Vous ne pouvez pas incliner le conteneur.

## 🔍 Exemples

**Exemple 1 :**
```
Input: height = [1,8,6,2,5,4,8,3,7]
Output: 49

Visualisation des murs :
     |
     |       |
     |   |   |
     | | | | | |
   | | | | | | | |
 | | | | | | | | |
[1,8,6,2,5,4,8,3,7]
 0 1 2 3 4 5 6 7 8  (indices)

Meilleure solution : murs à l'index 1 (hauteur=8) et index 8 (hauteur=7)
- Largeur = 8 - 1 = 7
- Hauteur = min(8, 7) = 7
- Surface = 7 × 7 = 49
```

**Exemple 2 :**
```
Input: height = [1,1]
Output: 1

Seulement deux murs de hauteur 1 :
- Largeur = 1 - 0 = 1
- Hauteur = min(1, 1) = 1
- Surface = 1 × 1 = 1
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