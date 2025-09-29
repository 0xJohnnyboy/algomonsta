# Exercice - Sliding Puzzle

**Niveau :** Hard
**Pattern :** BFS Graph
**Retour au cours :** [BFS on Graphs Pattern](../../courses/06-bfs-graph.md)

## 📝 Énoncé

Sur une grille `2x3`, il y a 5 tuiles étiquetées de `1` à `5`, et une tuile vide représentée par `0`.

Un **mouvement** consiste à choisir `0` et un voisin adjacent (4-directionnellement) et échanger leurs positions.

L'état **résolu** de ce puzzle est `[[1,2,3],[4,5,0]]`.

Étant donné la configuration initiale du puzzle `board`, retournez le *nombre minimum de mouvements* requis pour que l'état du puzzle soit résolu. S'il est impossible de résoudre le puzzle, retournez `-1`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: board = [[1,2,3],[4,0,5]]
Output: 1
Explication: Échangez la tuile vide avec 5 et résolvez le conseil.
```

**Exemple 2 :**
```
Input: board = [[1,2,3],[5,4,0]]
Output: -1
Explication: Aucune suite de mouvements ne résoudra le conseil.
```

**Exemple 3 :**
```
Input: board = [[4,1,2],[5,0,3]]
Output: 5
Explication:
5 est le plus petit nombre de mouvements qui résout le conseil.
Un exemple de chemin :
Après le mouvement 0: [[4,1,2],[5,0,3]]
Après le mouvement 1: [[4,1,2],[0,5,3]]
Après le mouvement 2: [[0,1,2],[4,5,3]]
Après le mouvement 3: [[1,0,2],[4,5,3]]
Après le mouvement 4: [[1,2,0],[4,5,3]]
Après le mouvement 5: [[1,2,3],[4,5,0]]
```

## 🎯 Contraintes

- `board.length == 2`
- `board[i].length == 3`
- `0 <= board[i][j] <= 5`
- Chaque valeur `board[i][j]` est **unique**.

## 💡 Indices

<details>
<summary>Indice 1</summary>

Convertissez la grille 2D en représentation 1D pour faciliter la manipulation et la recherche.

</details>

<details>
<summary>Indice 2</summary>

Utilisez BFS où chaque état du puzzle est un nœud et les mouvements valides créent les arêtes.

</details>

<details>
<summary>Indice 3</summary>

L'état cible est "123450". Trouvez la position de 0 et générez tous les mouvements possibles.

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "strconv"
    "strings"
)

func slidingPuzzle(board [][]int) int {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        board    [][]int
        expected int
    }{
        {
            [][]int{{1, 2, 3}, {4, 0, 5}},
            1,
        },
        {
            [][]int{{1, 2, 3}, {5, 4, 0}},
            -1,
        },
        {
            [][]int{{4, 1, 2}, {5, 0, 3}},
            5,
        },
        {
            [][]int{{1, 2, 3}, {4, 5, 0}},
            0,
        },
        {
            [][]int{{3, 2, 4}, {1, 5, 0}},
            14,
        },
        {
            [][]int{{0, 1, 3}, {4, 2, 5}},
            7,
        },
    }

    fmt.Println("=== Tests Sliding Puzzle ===")
    for i, test := range tests {
        result := slidingPuzzle(test.board)
        status := "✅"
        if result != test.expected {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Board: %v\n", test.board)
        fmt.Printf("  Expected: %d, Got: %d\n\n", test.expected, result)
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func slidingPuzzle(board [][]int) int {
    // Convertir le board en string pour faciliter la manipulation
    start := ""
    for i := 0; i < 2; i++ {
        for j := 0; j < 3; j++ {
            start += strconv.Itoa(board[i][j])
        }
    }

    target := "123450"
    if start == target {
        return 0
    }

    // Définir les voisins pour chaque position dans la représentation 1D
    // Position 0: voisins [1, 3]
    // Position 1: voisins [0, 2, 4]
    // Position 2: voisins [1, 5]
    // Position 3: voisins [0, 4]
    // Position 4: voisins [1, 3, 5]
    // Position 5: voisins [2, 4]
    neighbors := [][]int{
        {1, 3},    // position 0
        {0, 2, 4}, // position 1
        {1, 5},    // position 2
        {0, 4},    // position 3
        {1, 3, 5}, // position 4
        {2, 4},    // position 5
    }

    queue := []string{start}
    visited := make(map[string]bool)
    visited[start] = true
    moves := 0

    for len(queue) > 0 {
        size := len(queue)
        moves++

        for i := 0; i < size; i++ {
            current := queue[0]
            queue = queue[1:]

            // Trouver la position de 0
            zeroPos := strings.Index(current, "0")

            // Générer tous les mouvements possibles
            for _, neighbor := range neighbors[zeroPos] {
                newState := swap(current, zeroPos, neighbor)

                if newState == target {
                    return moves
                }

                if !visited[newState] {
                    visited[newState] = true
                    queue = append(queue, newState)
                }
            }
        }
    }

    return -1
}

func swap(s string, i, j int) string {
    bytes := []byte(s)
    bytes[i], bytes[j] = bytes[j], bytes[i]
    return string(bytes)
}
```

**Explication :**
1. **Représentation 1D** : Convertir la grille 2×3 en string de 6 caractères
2. **Graphe des voisins** : Pré-calculer les positions adjacentes pour chaque position
3. **BFS état par état** : Chaque configuration du puzzle est un état
4. **Génération des mouvements** : Échanger 0 avec ses voisins pour créer de nouveaux états

**Complexité :**
- Temps : O(6!) = O(720) - nombre maximum d'états possibles
- Espace : O(6!) = O(720) - pour visited et queue

</details>

## 🚀 Exercices similaires

- [Open the Lock](../medium/open-lock.md)
- [Word Ladder II](word-ladder-ii.md)
- [Word Ladder](../medium/word-ladder.md)