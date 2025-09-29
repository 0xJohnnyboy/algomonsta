# Exercice - 01 Matrix

**Niveau :** Medium
**Pattern :** BFS Multi-source
**Retour au cours :** [BFS on Graphs Pattern](../../courses/06-bfs-graph.md)

## 📝 Énoncé

Étant donné une matrice `m x n` remplie de `0` et `1`, trouvez la distance du `0` le plus proche pour chaque cellule.

La distance entre deux cellules adjacentes est `1`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: mat = [[0,0,0],[0,1,0],[0,0,0]]
Output: [[0,0,0],[0,1,0],[0,0,0]]
```

**Exemple 2 :**
```
Input: mat = [[0,0,0],[0,1,0],[1,1,1]]
Output: [[0,0,0],[0,1,0],[1,2,1]]
```

## 🎯 Contraintes

- `m == mat.length`
- `n == mat[i].length`
- `1 <= m, n <= 10^4`
- `1 <= m * n <= 10^4`
- `mat[i][j]` est soit `0` soit `1`.
- Il y a au moins un `0` dans `mat`.

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez BFS multi-source : commencez par tous les `0` simultanément.

</details>

<details>
<summary>Indice 2</summary>

Initialisez la distance des `0` à 0 et des `1` à l'infini, puis propagez les distances.

</details>

<details>
<summary>Indice 3</summary>

La première fois qu'on atteint une cellule en BFS, c'est forcément par le chemin le plus court.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func updateMatrix(mat [][]int) [][]int {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        input    [][]int
        expected [][]int
    }{
        {
            [][]int{{0, 0, 0}, {0, 1, 0}, {0, 0, 0}},
            [][]int{{0, 0, 0}, {0, 1, 0}, {0, 0, 0}},
        },
        {
            [][]int{{0, 0, 0}, {0, 1, 0}, {1, 1, 1}},
            [][]int{{0, 0, 0}, {0, 1, 0}, {1, 2, 1}},
        },
        {
            [][]int{{1, 1, 1}, {1, 1, 1}, {1, 1, 0}},
            [][]int{{4, 3, 2}, {3, 2, 1}, {2, 1, 0}},
        },
        {
            [][]int{{0}},
            [][]int{{0}},
        },
        {
            [][]int{{1}},
            [][]int{{1}},
        },
    }

    fmt.Println("=== Tests 01 Matrix ===")
    for i, test := range tests {
        result := updateMatrix(test.input)
        status := "✅"
        if !equalMatrix(result, test.expected) {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: %v\n", test.input)
        fmt.Printf("  Expected: %v\n", test.expected)
        fmt.Printf("  Got: %v\n\n", result)
    }
}

func equalMatrix(a, b [][]int) bool {
    if len(a) != len(b) {
        return false
    }
    for i := range a {
        if len(a[i]) != len(b[i]) {
            return false
        }
        for j := range a[i] {
            if a[i][j] != b[i][j] {
                return false
            }
        }
    }
    return true
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func updateMatrix(mat [][]int) [][]int {
    rows, cols := len(mat), len(mat[0])
    result := make([][]int, rows)
    for i := range result {
        result[i] = make([]int, cols)
    }

    queue := [][]int{}

    // Initialiser : 0 pour les zéros, -1 (non visité) pour les uns
    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            if mat[i][j] == 0 {
                result[i][j] = 0
                queue = append(queue, []int{i, j})
            } else {
                result[i][j] = -1
            }
        }
    }

    directions := [][]int{{-1, 0}, {1, 0}, {0, -1}, {0, 1}}

    // BFS multi-source
    for len(queue) > 0 {
        curr := queue[0]
        queue = queue[1:]
        row, col := curr[0], curr[1]

        for _, dir := range directions {
            newRow, newCol := row+dir[0], col+dir[1]

            // Vérifier les limites et si non visité
            if newRow >= 0 && newRow < rows &&
               newCol >= 0 && newCol < cols &&
               result[newRow][newCol] == -1 {

                result[newRow][newCol] = result[row][col] + 1
                queue = append(queue, []int{newRow, newCol})
            }
        }
    }

    return result
}
```

**Explication :**
1. **Multi-source BFS** : Commencez par ajouter tous les `0` à la queue
2. **Initialisation** : Distance 0 pour les zéros, -1 (non visité) pour les uns
3. **Propagation** : Chaque cellule atteinte reçoit la distance de son parent + 1
4. **Garantie d'optimalité** : BFS garantit le chemin le plus court

**Complexité :**
- Temps : O(m × n) - chaque cellule est visitée une fois
- Espace : O(m × n) - pour la queue et le résultat

</details>

## 🚀 Exercices similaires

- [Rotting Oranges](rotting-oranges.md)
- [Number of Islands](../easy/num-islands.md)
- [Word Ladder](word-ladder.md)