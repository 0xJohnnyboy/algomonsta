# Exercice - Largest Rectangle in Histogram

**Niveau :** Medium
**Pattern :** Monotonic Stack
**Retour au cours :** [Monotonic Stack Pattern](../../courses/04-stack-monotonic.md)

## 📝 Énoncé

Étant donné un tableau d'entiers `heights` représentant la hauteur des barres dans un histogramme où la largeur de chaque barre est 1, retournez la surface du plus grand rectangle dans l'histogramme.

## 🔍 Exemples

**Exemple 1 :**
```
Input: heights = [2,1,5,6,2,3]
Output: 10
Explication: Le plus grand rectangle a une surface de 10 unités.
Il s'étend de l'indice 2 à 4 avec hauteur = 5.
```

```
    6
  5 █ 6
  █ █ █     3
2 █ █ █ 2 3 █
█ █ █ █ █ █ █
0 1 2 3 4 5 6
```

**Exemple 2 :**
```
Input: heights = [2,4]
Output: 4
Explication: Rectangle de hauteur 2 et largeur 2, ou hauteur 4 et largeur 1.
```

**Exemple 3 :**
```
Input: heights = [1,1,1,1]
Output: 4
Explication: Rectangle de hauteur 1 et largeur 4.
```

## 🎯 Contraintes

- `1 <= heights.length <= 10^5`
- `0 <= heights[i] <= 10^4`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Pour chaque barre, trouvez la surface maximale d'un rectangle qui a cette barre comme hauteur minimale. Cela nécessite de connaître les bornes gauche et droite où cette hauteur peut s'étendre.

</details>

<details>
<summary>Indice 2</summary>

Utilisez une monotonic stack croissante. Quand vous rencontrez une barre plus petite, elle définit la borne droite pour toutes les barres plus hautes dans la stack.

</details>

<details>
<summary>Indice 3</summary>

Pour chaque barre poppée, calculez la surface : `hauteur × largeur`. La largeur est déterminée par les indices des barres adjacentes dans la stack.

</details>

<details>
<summary>Indice 4</summary>

Ajoutez un élément 0 à la fin du tableau pour forcer le traitement des barres restantes dans la stack.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func largestRectangleArea(heights []int) int {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println(largestRectangleArea([]int{2, 1, 5, 6, 2, 3}))
    // Expected: 10

    fmt.Println(largestRectangleArea([]int{2, 4}))
    // Expected: 4

    fmt.Println(largestRectangleArea([]int{1, 1, 1, 1}))
    // Expected: 4

    fmt.Println(largestRectangleArea([]int{0}))
    // Expected: 0

    fmt.Println(largestRectangleArea([]int{5}))
    // Expected: 5

    fmt.Println(largestRectangleArea([]int{2, 1, 2}))
    // Expected: 3

    fmt.Println(largestRectangleArea([]int{6, 2, 5, 4, 5, 1, 6}))
    // Expected: 12

    fmt.Println(largestRectangleArea([]int{1, 2, 3, 4, 5}))
    // Expected: 9

    fmt.Println(largestRectangleArea([]int{5, 4, 3, 2, 1}))
    // Expected: 9
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func largestRectangleArea(heights []int) int {
    stack := []int{}    // Stack d'indices (monotonic croissante)
    maxArea := 0

    // Parcourir toutes les barres + une barre virtuelle de hauteur 0
    for i := 0; i <= len(heights); i++ {
        // Hauteur actuelle (0 pour la barre virtuelle finale)
        currentHeight := 0
        if i < len(heights) {
            currentHeight = heights[i]
        }

        // Pop toutes les barres plus hautes que la barre actuelle
        for len(stack) > 0 && heights[stack[len(stack)-1]] > currentHeight {
            // Calculer la surface avec la barre poppée comme hauteur
            height := heights[stack[len(stack)-1]]
            stack = stack[:len(stack)-1]

            // Largeur du rectangle
            var width int
            if len(stack) == 0 {
                // Le rectangle s'étend depuis le début
                width = i
            } else {
                // Le rectangle s'étend entre les barres adjacentes
                width = i - stack[len(stack)-1] - 1
            }

            area := height * width
            if area > maxArea {
                maxArea = area
            }
        }

        // Push l'indice actuel (sauf pour la barre virtuelle)
        if i < len(heights) {
            stack = append(stack, i)
        }
    }

    return maxArea
}
```

**Simulation détaillée pour `[2,1,5,6,2,3]` :**

```
Ajout barre virtuelle: [2,1,5,6,2,3,0]

i=0, height=2: stack=[] → push 0 → stack=[0]
i=1, height=1: stack=[0], heights[0]=2>1
    → pop 0: hauteur=2, width=1-0=1, area=2×1=2, maxArea=2
    → push 1 → stack=[1]

i=2, height=5: stack=[1], heights[1]=1<5 → push 2 → stack=[1,2]
i=3, height=6: stack=[1,2], heights[2]=5<6 → push 3 → stack=[1,2,3]
i=4, height=2: stack=[1,2,3], heights[3]=6>2
    → pop 3: hauteur=6, width=4-2-1=1, area=6×1=6, maxArea=6
    → stack=[1,2], heights[2]=5>2
    → pop 2: hauteur=5, width=4-1-1=2, area=5×2=10, maxArea=10
    → stack=[1], heights[1]=1<2 → push 4 → stack=[1,4]

i=5, height=3: stack=[1,4], heights[4]=2<3 → push 5 → stack=[1,4,5]
i=6, height=0: stack=[1,4,5]
    → pop 5: hauteur=3, width=6-4-1=1, area=3×1=3, maxArea=10
    → pop 4: hauteur=2, width=6-1-1=4, area=2×4=8, maxArea=10
    → pop 1: hauteur=1, width=6-0=6, area=1×6=6, maxArea=10

Résultat: 10
```

**Explication visuelle :**
```
Rectangle optimal (surface=10):
    6
  5 █ 6
  █ █ █     3
2 █ █ █ 2 3 █
█ █ █ █ █ █ █
0 1 2 3 4 5 6
    [---]     <- Rectangle de hauteur 5, largeur 2
```

**Explication de l'algorithme :**

1. **Monotonic Stack Croissante** : maintient les indices des barres en ordre croissant de hauteur
2. **Calcul lors du pop** : quand une barre est poppée, elle devient la hauteur du rectangle
3. **Calcul de largeur** :
   - Si stack vide : largeur = indice actuel
   - Sinon : largeur = indice actuel - indice précédent dans stack - 1
4. **Barre virtuelle** : hauteur 0 à la fin pour vider complètement la stack

**Complexité :**
- Temps : O(n) - chaque élément est pushé et poppé au plus une fois
- Espace : O(n) - stack dans le pire cas (hauteurs croissantes)

**Intuition clé :**
- Pour chaque barre, on cherche le plus grand rectangle qui a cette barre comme **hauteur minimale**
- La stack maintient les barres candidates (en ordre croissant)
- Quand on rencontre une barre plus petite, elle "ferme" les rectangles des barres plus hautes

</details>

## 🎯 Points clés à retenir

1. **Monotonic Stack Croissante** : différent des autres exercices (ici croissante, pas décroissante)
2. **Calcul lors du pop** : chaque barre poppée définit la hauteur d'un rectangle candidat
3. **Calcul de largeur** : utilise les indices dans la stack pour déterminer les bornes
4. **Barre virtuelle** : technique pour forcer le traitement des éléments restants

## 🚀 Exercices similaires

- [Maximal Rectangle](../hard/largest-rectangle-matrix.md) - Extension 2D de ce problème
- [Trapping Rain Water](../hard/trapping-rain-water.md) - Autre problème géométrique avec stack
- [Daily Temperatures](./daily-temperatures.md) - Pattern monotonic stack basique

## 🔍 Variations

**Version avec stack de structures :**
```go
type Bar struct {
    height int
    index  int
}

func largestRectangleArea(heights []int) int {
    stack := []Bar{}
    // Utiliser des structures au lieu d'indices seuls
}
```

**Version récursive (divide and conquer) :**
```go
func largestRectangleArea(heights []int) int {
    return divideConquer(heights, 0, len(heights)-1)
}

func divideConquer(heights []int, left, right int) int {
    if left > right {
        return 0
    }

    // Trouver la hauteur minimale
    minIndex := left
    for i := left + 1; i <= right; i++ {
        if heights[i] < heights[minIndex] {
            minIndex = i
        }
    }

    // Trois candidats: gauche, droite, ou rectangle complet
    minArea := heights[minIndex] * (right - left + 1)
    leftArea := divideConquer(heights, left, minIndex-1)
    rightArea := divideConquer(heights, minIndex+1, right)

    return max(minArea, max(leftArea, rightArea))
}
```