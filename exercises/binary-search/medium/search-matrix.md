# Exercice - Search a 2D Matrix

**Niveau :** Medium
**Pattern :** Binary Search in 2D Array
**Retour au cours :** [Binary Search Pattern](../../courses/03-binary-search.md)

## 📝 Énoncé

Écrivez un algorithme efficace qui recherche une valeur `target` dans une matrice `m x n` d'entiers. Cette matrice a les propriétés suivantes :

- Les entiers de chaque ligne sont triés de gauche à droite
- Le premier entier de chaque ligne est supérieur au dernier entier de la ligne précédente

## 🔍 Exemples

**Exemple 1 :**
```
Input: matrix = [[1,4,7,11,15],
                [2,5,8,12,19],
                [3,6,9,16,22],
                [10,13,14,17,24],
                [18,21,23,26,30]], target = 5
Output: true
```

**Exemple 2 :**
```
Input: matrix = [[1,4,7,11,15],
                [2,5,8,12,19],
                [3,6,9,16,22],
                [10,13,14,17,24],
                [18,21,23,26,30]], target = 20
Output: false
```

**Exemple 3 :**
```
Input: matrix = [[1]], target = 1
Output: true
```

## 🎯 Contraintes

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 100`
- `-10^4 <= matrix[i][j], target <= 10^4`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Étant donné les propriétés de tri, vous pouvez traiter la matrice 2D comme un tableau 1D trié.

</details>

<details>
<summary>Indice 2</summary>

Si vous imaginez "dérouler" la matrice en un tableau 1D, comment convertir un index 1D en coordonnées 2D ?

</details>

<details>
<summary>Indice 3</summary>

Pour un index `idx` dans un tableau 1D de taille m×n :
- row = idx / n
- col = idx % n

</details>

<details>
<summary>Indice 4</summary>

Utilisez binary search classique sur cet "array virtuel 1D" avec conversion d'indices.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func searchMatrix(matrix [][]int, target int) bool {
    // Votre code ici

}

func main() {
    // Test cases
    matrix1 := [][]int{
        {1, 4, 7, 11, 15},
        {2, 5, 8, 12, 19},
        {3, 6, 9, 16, 22},
        {10, 13, 14, 17, 24},
        {18, 21, 23, 26, 30},
    }

    fmt.Println(searchMatrix(matrix1, 5))  // Expected: true
    fmt.Println(searchMatrix(matrix1, 20)) // Expected: false

    matrix2 := [][]int{{1}}
    fmt.Println(searchMatrix(matrix2, 1))  // Expected: true
    fmt.Println(searchMatrix(matrix2, 0))  // Expected: false

    matrix3 := [][]int{{1, 3, 5, 7}, {10, 11, 16, 20}, {23, 30, 34, 60}}
    fmt.Println(searchMatrix(matrix3, 3))  // Expected: true
    fmt.Println(searchMatrix(matrix3, 13)) // Expected: false

    matrix4 := [][]int{{1, 4}, {2, 5}}
    fmt.Println(searchMatrix(matrix4, 2))  // Expected: true

    matrix5 := [][]int{{1, 1}}
    fmt.Println(searchMatrix(matrix5, 2))  // Expected: false
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func searchMatrix(matrix [][]int, target int) bool {
    if len(matrix) == 0 || len(matrix[0]) == 0 {
        return false
    }

    m, n := len(matrix), len(matrix[0])
    left, right := 0, m*n-1

    for left <= right {
        mid := left + (right-left)/2

        // Convertir l'index 1D en coordonnées 2D
        row := mid / n
        col := mid % n
        midValue := matrix[row][col]

        if midValue == target {
            return true
        } else if midValue < target {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }

    return false
}
```

**Explication détaillée :**

1. **Observation clé :** La matrice peut être vue comme un array 1D trié

2. **Mapping des indices :**
   - Array 1D de taille `m × n` : indices `[0, m×n-1]`
   - Pour index `idx` : `row = idx / n`, `col = idx % n`

3. **Binary search classique :**
   - `left = 0`, `right = m×n-1`
   - À chaque `mid`, convertir en coordonnées 2D
   - Comparer `matrix[row][col]` avec `target`

**Exemple de trace pour la première matrice, target = 5 :**

```
Matrix 5×5 → Array 1D de taille 25
left=0, right=24

mid=12 → row=12/5=2, col=12%5=2 → matrix[2][2]=9 > 5 → right=11
mid=5 → row=5/5=1, col=5%5=0 → matrix[1][0]=2 < 5 → left=6
mid=8 → row=8/5=1, col=8%5=3 → matrix[1][3]=12 > 5 → right=7
mid=6 → row=6/5=1, col=6%5=1 → matrix[1][1]=5 == 5 → trouvé !
```

**Complexité :**
- Temps : O(log(m×n)) = O(log m + log n)
- Espace : O(1)

**Alternative - Deux binary searches :**

```go
func searchMatrix(matrix [][]int, target int) bool {
    // Étape 1 : Trouver la ligne
    m, n := len(matrix), len(matrix[0])
    top, bottom := 0, m-1

    for top <= bottom {
        mid := top + (bottom-top)/2
        if matrix[mid][0] <= target && target <= matrix[mid][n-1] {
            // Étape 2 : Chercher dans cette ligne
            left, right := 0, n-1
            for left <= right {
                col := left + (right-left)/2
                if matrix[mid][col] == target {
                    return true
                } else if matrix[mid][col] < target {
                    left = col + 1
                } else {
                    right = col - 1
                }
            }
            return false
        } else if matrix[mid][0] > target {
            bottom = mid - 1
        } else {
            top = mid + 1
        }
    }

    return false
}
```

**Points clés :**
- Traitement 2D → 1D avec mapping d'indices
- Binary search standard sur array virtuel
- Alternative avec double binary search (même complexité)

</details>

## 🚀 Exercices similaires

- [Search a 2D Matrix II](../medium/search-matrix-ii.md) - Propriétés de tri différentes
- [Binary Search](../easy/binary-search.md) - Version de base
- [Find Peak Element II](../hard/find-peak-2d.md) - Autre problème 2D

## 🎓 Concepts clés

- **Array 2D → 1D** : mapping d'indices pour simplifier
- **Coordonnées virtuelles** : `row = idx/n`, `col = idx%n`
- **Binary Search étendu** : même principe sur structure différente
- **Propriétés de tri** : exploiter l'organisation des données