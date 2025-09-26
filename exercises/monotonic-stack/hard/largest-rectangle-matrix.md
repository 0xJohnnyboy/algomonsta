# Exercice - Maximal Rectangle

**Niveau :** Hard
**Pattern :** Monotonic Stack
**Retour au cours :** [Monotonic Stack Pattern](../../courses/04-stack-monotonic.md)

## 📝 Énoncé

Étant donné une matrice binaire `matrix` remplie de `'0'` et `'1'`, trouvez le plus grand rectangle contenant uniquement des `'1'` et retournez sa surface.

## 🔍 Exemples

**Exemple 1 :**
```
Input: matrix = [
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
Output: 6
Explication: Le rectangle maximal est montré dans la matrice ci-dessus.
```

```
Visualisation:
1 0 1 0 0
1 0 ■ ■ ■  ← Rectangle de surface 6
1 1 ■ ■ ■
1 0 0 1 0
```

**Exemple 2 :**
```
Input: matrix = [["0"]]
Output: 0
```

**Exemple 3 :**
```
Input: matrix = [["1"]]
Output: 1
```

## 🎯 Contraintes

- `rows == matrix.length`
- `cols == matrix[i].length`
- `1 <= row, cols <= 200`
- `matrix[i][j]` est `'0'` ou `'1'`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Transformez ce problème 2D en plusieurs problèmes 1D "Largest Rectangle in Histogram". Pour chaque ligne, calculez les hauteurs des histogrammes.

</details>

<details>
<summary>Indice 2</summary>

Pour chaque ligne, maintenez un tableau `heights` où `heights[j]` est la hauteur de la colonne `j` à la ligne actuelle (nombre consécutif de '1' finissant à cette position).

</details>

<details>
<summary>Indice 3</summary>

Pour chaque ligne, appliquez l'algorithme "Largest Rectangle in Histogram" avec monotonic stack sur le tableau des hauteurs.

</details>

<details>
<summary>Indice 4</summary>

Mise à jour des hauteurs : si `matrix[i][j] == '1'`, alors `heights[j]++`; sinon `heights[j] = 0`.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func maximalRectangle(matrix [][]byte) int {
    // Votre code ici

}

func largestRectangleArea(heights []int) int {
    // Réutilisez la solution de "Largest Rectangle in Histogram"

}

func main() {
    // Test cases
    fmt.Println(maximalRectangle([][]byte{
        {'1', '0', '1', '0', '0'},
        {'1', '0', '1', '1', '1'},
        {'1', '1', '1', '1', '1'},
        {'1', '0', '0', '1', '0'},
    }))
    // Expected: 6

    fmt.Println(maximalRectangle([][]byte{
        {'0'},
    }))
    // Expected: 0

    fmt.Println(maximalRectangle([][]byte{
        {'1'},
    }))
    // Expected: 1

    fmt.Println(maximalRectangle([][]byte{
        {'1', '1', '1'},
        {'1', '1', '1'},
        {'1', '1', '1'},
    }))
    // Expected: 9

    fmt.Println(maximalRectangle([][]byte{
        {'0', '0', '0'},
        {'0', '0', '0'},
        {'0', '0', '0'},
    }))
    // Expected: 0

    fmt.Println(maximalRectangle([][]byte{
        {'1', '0'},
        {'1', '1'},
        {'0', '1'},
    }))
    // Expected: 2

    fmt.Println(maximalRectangle([][]byte{
        {'1', '1', '0', '1'},
        {'1', '1', '0', '1'},
        {'1', '1', '1', '1'},
    }))
    // Expected: 4

    fmt.Println(maximalRectangle([][]byte{
        {'0', '1', '1', '0', '1'},
        {'1', '1', '0', '1', '0'},
        {'0', '1', '1', '1', '0'},
        {'1', '1', '1', '1', '0'},
        {'1', '1', '1', '1', '1'},
        {'0', '0', '0', '0', '0'},
    }))
    // Expected: 8
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func maximalRectangle(matrix [][]byte) int {
    if len(matrix) == 0 || len(matrix[0]) == 0 {
        return 0
    }

    rows, cols := len(matrix), len(matrix[0])
    heights := make([]int, cols) // Hauteurs de l'histogramme pour chaque colonne
    maxArea := 0

    // Traiter chaque ligne comme la base d'un histogramme
    for i := 0; i < rows; i++ {
        // Mettre à jour les hauteurs pour la ligne actuelle
        for j := 0; j < cols; j++ {
            if matrix[i][j] == '1' {
                heights[j]++ // Incrémenter la hauteur
            } else {
                heights[j] = 0 // Réinitialiser à 0
            }
        }

        // Calculer la surface maximale pour cette ligne
        area := largestRectangleArea(heights)
        if area > maxArea {
            maxArea = area
        }
    }

    return maxArea
}

func largestRectangleArea(heights []int) int {
    stack := []int{}
    maxArea := 0

    for i := 0; i <= len(heights); i++ {
        currentHeight := 0
        if i < len(heights) {
            currentHeight = heights[i]
        }

        for len(stack) > 0 && heights[stack[len(stack)-1]] > currentHeight {
            height := heights[stack[len(stack)-1]]
            stack = stack[:len(stack)-1]

            var width int
            if len(stack) == 0 {
                width = i
            } else {
                width = i - stack[len(stack)-1] - 1
            }

            area := height * width
            if area > maxArea {
                maxArea = area
            }
        }

        if i < len(heights) {
            stack = append(stack, i)
        }
    }

    return maxArea
}
```

**Simulation détaillée pour la matrice d'exemple :**

```
Matrix:
["1","0","1","0","0"]
["1","0","1","1","1"]
["1","1","1","1","1"]
["1","0","0","1","0"]

Ligne 0: matrix[0] = [1,0,1,0,0]
heights = [1,0,1,0,0]
largestRectangleArea([1,0,1,0,0]) = 1

Ligne 1: matrix[1] = [1,0,1,1,1]
heights[0] = 1+1 = 2 (car matrix[1][0] = '1')
heights[1] = 0 (car matrix[1][1] = '0')
heights[2] = 1+1 = 2 (car matrix[1][2] = '1')
heights[3] = 0+1 = 1 (car matrix[1][3] = '1')
heights[4] = 0+1 = 1 (car matrix[1][4] = '1')
heights = [2,0,2,1,1]
largestRectangleArea([2,0,2,1,1]) = 2

Ligne 2: matrix[2] = [1,1,1,1,1]
heights = [3,1,3,2,2]
largestRectangleArea([3,1,3,2,2]) = 6 ← Maximum trouvé!

Ligne 3: matrix[3] = [1,0,0,1,0]
heights = [4,0,0,3,0]
largestRectangleArea([4,0,0,3,0]) = 4

Résultat final: 6
```

**Visualisation de l'histogramme optimal (ligne 2) :**
```
Histogramme à la ligne 2: heights = [3,1,3,2,2]

Hauteur:
3  █   █
2  █   █ █ █
1  █ █ █ █ █
0  █████████████
   0 1 2 3 4

Rectangle optimal: hauteur = 2, largeur = 3 (colonnes 2, 3, 4)
Surface = 2 × 3 = 6
```

**Correspondance avec la matrice originale :**
```
Matrice avec rectangle optimal surligné:
1 0 ■ 0 0  ← Ligne 0 (heights = [1,0,1,0,0])
1 0 ■ ■ ■  ← Ligne 1 (heights = [2,0,2,1,1])
1 1 ■ ■ ■  ← Ligne 2 (heights = [3,1,3,2,2])
1 0 0 1 0  ← Ligne 3 (heights = [4,0,0,3,0])

Le rectangle ■ a une surface de 6 (hauteur 2 × largeur 3)
```

**Explication de l'algorithme :**

1. **Transformation en histogrammes** : chaque ligne devient la base d'un histogramme
2. **Mise à jour des hauteurs** : accumule les '1' consécutifs verticalement
3. **Réinitialisation** : '0' remet la hauteur à zéro (coupe la séquence)
4. **Application de l'algo 1D** : utilise "Largest Rectangle in Histogram" pour chaque ligne

**Complexité :**
- Temps : O(rows × cols) - chaque cellule traitée en temps constant amorti
- Espace : O(cols) - tableau des hauteurs et stack

**Pourquoi ça marche :**
- Chaque rectangle possible dans la matrice correspond à un rectangle dans au moins un des histogrammes
- La transformation préserve toutes les solutions optimales
- L'algorithme 1D trouve efficacement le meilleur rectangle pour chaque histogramme

</details>

## 🎯 Points clés à retenir

1. **Réduction de dimension** : problème 2D → série de problèmes 1D
2. **Accumulation des hauteurs** : maintenir l'état des colonnes entre les lignes
3. **Réutilisation d'algorithme** : appliquer "Largest Rectangle in Histogram"
4. **Complexité optimale** : O(rows × cols) grâce au monotonic stack

## 🚀 Exercices similaires

- [Largest Rectangle in Histogram](../medium/largest-rectangle.md) - Base de cet algorithme
- [Maximal Square](../medium/maximal-square.md) - Variant carré au lieu de rectangle
- [Number of Submatrices That Sum to Target](../hard/submatrix-sum.md) - Autre réduction 2D→1D

## 🔍 Variations

**Version Maximal Square :**
```go
func maximalSquare(matrix [][]byte) int {
    // Même principe mais on limite width = height
    if len(matrix) == 0 || len(matrix[0]) == 0 {
        return 0
    }

    rows, cols := len(matrix), len(matrix[0])
    heights := make([]int, cols)
    maxSide := 0

    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            if matrix[i][j] == '1' {
                heights[j]++
            } else {
                heights[j] = 0
            }
        }

        side := largestSquareArea(heights)
        if side > maxSide {
            maxSide = side
        }
    }

    return maxSide * maxSide
}
```

**Version avec DP (alternative) :**
```go
func maximalRectangleDP(matrix [][]byte) int {
    if len(matrix) == 0 || len(matrix[0]) == 0 {
        return 0
    }

    rows, cols := len(matrix), len(matrix[0])
    heights := make([]int, cols)
    left := make([]int, cols)
    right := make([]int, cols)
    maxArea := 0

    for i := range right {
        right[i] = cols
    }

    for i := 0; i < rows; i++ {
        // Mettre à jour heights, left, right
        // Calculer maxArea
    }

    return maxArea
}
```

**Version Count Rectangles :**
```go
func countMaximalRectangles(matrix [][]byte) int {
    // Compter tous les rectangles maximaux, pas seulement la surface
    // Modifications de l'algorithme principal pour tracker les coordonnées
}
```