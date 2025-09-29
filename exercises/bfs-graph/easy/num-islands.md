# Exercice - Number of Islands

**Niveau :** Easy
**Pattern :** BFS Composantes connexes
**Retour au cours :** [BFS on Graphs Pattern](../../courses/06-bfs-graph.md)

## 📝 Énoncé

Étant donnée une grille 2D `m x n` de caractères `'1'` (terre) et `'0'` (eau), retournez le **nombre d'îles**.

Une île est formée en connectant horizontalement ou verticalement des terres adjacentes et est entourée d'eau. Vous pouvez supposer que les quatre bords de la grille sont tous entourés d'eau.

## 🔍 Exemples

**Exemple 1 :**
```
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

**Exemple 2 :**
```
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

**Exemple 3 :**
```
Input: grid = [["0"]]
Output: 0
```

**Exemple 4 :**
```
Input: grid = [["1"]]
Output: 1
```

## 🎯 Contraintes

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `grid[i][j]` est `'0'` ou `'1'`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Chaque île est une **composante connexe** de cellules terrestres. Utilisez BFS pour explorer complètement chaque île.

</details>

<details>
<summary>Indice 2</summary>

Parcourez la grille, et à chaque fois que vous trouvez une cellule `'1'` non visitée, lancez un BFS pour marquer toute l'île.

</details>

<details>
<summary>Indice 3</summary>

Utilisez BFS pour explorer les 4 directions (haut, bas, gauche, droite) et marquer les cellules visitées pour éviter de les compter plusieurs fois.

</details>

<details>
<summary>Indice 4</summary>

Modifiez la grille directement (remplacez `'1'` par `'0'`) ou utilisez un tableau `visited` séparé pour marquer les cellules explorées.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func numIslands(grid [][]byte) int {
    // Votre code ici

}

func main() {
    // Test cases
    grid1 := [][]byte{
        {'1', '1', '1', '1', '0'},
        {'1', '1', '0', '1', '0'},
        {'1', '1', '0', '0', '0'},
        {'0', '0', '0', '0', '0'},
    }
    fmt.Println("Exemple 1:", numIslands(grid1)) // Expected: 1

    grid2 := [][]byte{
        {'1', '1', '0', '0', '0'},
        {'1', '1', '0', '0', '0'},
        {'0', '0', '1', '0', '0'},
        {'0', '0', '0', '1', '1'},
    }
    fmt.Println("Exemple 2:", numIslands(grid2)) // Expected: 3

    grid3 := [][]byte{{'0'}}
    fmt.Println("Exemple 3:", numIslands(grid3)) // Expected: 0

    grid4 := [][]byte{{'1'}}
    fmt.Println("Exemple 4:", numIslands(grid4)) // Expected: 1

    grid5 := [][]byte{
        {'1', '0', '1'},
        {'0', '1', '0'},
        {'1', '0', '1'},
    }
    fmt.Println("Exemple 5:", numIslands(grid5)) // Expected: 5

    grid6 := [][]byte{
        {'1', '1', '1'},
        {'1', '0', '1'},
        {'1', '1', '1'},
    }
    fmt.Println("Exemple 6:", numIslands(grid6)) // Expected: 1

    grid7 := [][]byte{
        {'1', '1', '0', '1'},
        {'0', '0', '0', '1'},
        {'1', '0', '1', '1'},
    }
    fmt.Println("Exemple 7:", numIslands(grid7)) // Expected: 3

    grid8 := [][]byte{
        {'0', '0', '0'},
        {'0', '0', '0'},
        {'0', '0', '0'},
    }
    fmt.Println("Exemple 8:", numIslands(grid8)) // Expected: 0
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func numIslands(grid [][]byte) int {
    if len(grid) == 0 || len(grid[0]) == 0 {
        return 0
    }

    rows, cols := len(grid), len(grid[0])
    count := 0
    directions := [][2]int{{-1, 0}, {1, 0}, {0, -1}, {0, 1}}

    // Fonction BFS pour explorer une île complète
    bfs := func(startRow, startCol int) {
        queue := [][2]int{{startRow, startCol}}
        grid[startRow][startCol] = '0' // Marquer comme visité

        for len(queue) > 0 {
            current := queue[0]
            queue = queue[1:]

            // Explorer les 4 directions
            for _, dir := range directions {
                newRow := current[0] + dir[0]
                newCol := current[1] + dir[1]

                // Vérifier les limites et si c'est de la terre non visitée
                if newRow >= 0 && newRow < rows &&
                   newCol >= 0 && newCol < cols &&
                   grid[newRow][newCol] == '1' {

                    grid[newRow][newCol] = '0' // Marquer comme visité
                    queue = append(queue, [2]int{newRow, newCol})
                }
            }
        }
    }

    // Parcourir toute la grille
    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            if grid[i][j] == '1' {
                // Nouvelle île trouvée
                count++
                bfs(i, j) // Explorer toute l'île
            }
        }
    }

    return count
}
```

**Simulation détaillée pour l'exemple 2 :**

```
Grille initiale: [["1","1","0","0","0"],
                  ["1","1","0","0","0"],
                  ["0","0","1","0","0"],
                  ["0","0","0","1","1"]]

Parcours:
Position (0,0): '1' trouvé → count = 1
- BFS explore (0,0), (0,1), (1,0), (1,1)
- Grille après BFS: [["0","0","0","0","0"],
                     ["0","0","0","0","0"],
                     ["0","0","1","0","0"],
                     ["0","0","0","1","1"]]

Position (2,2): '1' trouvé → count = 2
- BFS explore seulement (2,2)
- Grille après BFS: [["0","0","0","0","0"],
                     ["0","0","0","0","0"],
                     ["0","0","0","0","0"],
                     ["0","0","0","1","1"]]

Position (3,3): '1' trouvé → count = 3
- BFS explore (3,3) et (3,4)
- Grille finale: [["0","0","0","0","0"],
                  ["0","0","0","0","0"],
                  ["0","0","0","0","0"],
                  ["0","0","0","0","0"]]

Résultat: 3 îles
```

**Points clés de l'algorithme :**

1. **Détection de composantes connexes** : Chaque BFS trouve une île complète
2. **Modification in-place** : Marquer `'1'` → `'0'` évite un tableau visited
3. **Exploration complète** : BFS garantit qu'on visite toute l'île d'un coup
4. **Comptage simple** : Une nouvelle terre = une nouvelle île

**Complexité :**
- Temps : O(m × n) - chaque cellule est visitée au maximum une fois
- Espace : O(min(m, n)) - taille maximale de la queue dans le pire cas

**Pourquoi BFS et pas DFS ?**
- Les deux fonctionnent pour ce problème
- BFS utilise une queue, DFS utilise la pile (récursion ou stack explicite)
- BFS peut être plus préférable pour de très grandes grilles (évite stack overflow)

</details>

## 🎯 Points clés à retenir

1. **Composantes connexes** : Chaque île est une composante connexe à explorer complètement
2. **Modification in-place** : Utiliser la grille elle-même pour marquer les cellules visitées
3. **Pattern d'exploration** : Parcourir la grille + BFS sur chaque nouvelle composante
4. **Directions 4-connexes** : Seules les connexions horizontales/verticales comptent

## 🚀 Exercices similaires

- [Max Area of Island](../medium/max-area-island.md) - Trouver la plus grande île
- [Surrounded Regions](../medium/surrounded-regions.md) - Capturer les régions entourées
- [Pacific Atlantic Water Flow](../medium/pacific-atlantic.md) - Flot d'eau vers deux océans

## 🔍 Variations

**Version avec tableau visited séparé :**
```go
visited := make([][]bool, rows)
for i := range visited {
    visited[i] = make([]bool, cols)
}
// Vérifier visited[newRow][newCol] au lieu de modifier grid
```

**Version récursive DFS :**
```go
func dfs(grid [][]byte, i, j int) {
    if i < 0 || i >= len(grid) || j < 0 || j >= len(grid[0]) || grid[i][j] == '0' {
        return
    }
    grid[i][j] = '0'
    dfs(grid, i-1, j) // haut
    dfs(grid, i+1, j) // bas
    dfs(grid, i, j-1) // gauche
    dfs(grid, i, j+1) // droite
}
```

**Optimisation pour grilles très grandes :**
```go
// Utiliser des coordonnées packées dans un int64
packed := int64(row)<<32 | int64(col)
```