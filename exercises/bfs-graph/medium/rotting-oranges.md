# Exercice - Rotting Oranges

**Niveau :** Medium  
**Pattern :** BFS Multi-source  
**Retour au cours :** [BFS on Graphs Pattern](../../courses/06-bfs-graph.md)

## 📝 Énoncé

Vous avez une grille 2D `m x n` où :
- `0` représente une cellule vide
- `1` représente une orange fraîche  
- `2` représente une orange pourrie

Chaque minute, toute orange fraîche adjacente (4-directionnellement) à une orange pourrie devient pourrie.

Retournez le **nombre minimum de minutes** qui doivent s'écouler jusqu'à ce qu'aucune cellule n'ait d'orange fraîche. S'il est **impossible** de faire pourrir toutes les oranges, retournez `-1`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: grid = [[2,1,1],[1,1,0],[0,1,1]]
Output: 4

Minute 0: [[2,1,1],    Minute 1: [[2,2,1],
          [1,1,0],              [2,1,0], 
          [0,1,1]]              [0,1,1]]

Minute 2: [[2,2,2],    Minute 3: [[2,2,2],
          [2,2,0],              [2,2,0],
          [0,1,1]]              [0,2,1]]

Minute 4: [[2,2,2],
          [2,2,0],
          [0,2,2]]
```

**Exemple 2 :**
```
Input: grid = [[2,1,1],[0,1,1],[1,0,1]]
Output: -1
Explication: L'orange en bas à gauche (1,0) ne sera jamais pourrie car elle n'est pas connectée aux oranges pourries par des chemins 4-directionnels.
```

**Exemple 3 :**
```
Input: grid = [[0,2]]
Output: 0
Explication: Puisque qu'il n'y a déjà aucune orange fraîche à la minute 0, la réponse est 0.
```

## 🎯 Contraintes

- `m == grid.length`
- `n == grid[i].length`  
- `1 <= m, n <= 10`
- `grid[i][j]` est `0`, `1`, ou `2`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème de **BFS multi-source**. Toutes les oranges pourries commencent à se propager simultanément.

</details>

<details>
<summary>Indice 2</summary>

Ajoutez toutes les oranges pourries initiales dans la queue en même temps. Comptez aussi le nombre d'oranges fraîches.

</details>

<details>
<summary>Indice 3</summary>

Utilisez le pattern BFS niveau par niveau pour compter les minutes. Chaque niveau représente une minute.

</details>

<details>
<summary>Indice 4</summary>

Après chaque minute, vérifiez si toutes les oranges fraîches ont été infectées. Sinon, continuez ou retournez -1.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func orangesRotting(grid [][]int) int {
    // Votre code ici
    
}

func main() {
    // Test cases
    grid1 := [][]int{
        {2, 1, 1},
        {1, 1, 0},
        {0, 1, 1},
    }
    fmt.Println("Exemple 1:", orangesRotting(grid1)) // Expected: 4
    
    grid2 := [][]int{
        {2, 1, 1},
        {0, 1, 1},
        {1, 0, 1},
    }
    fmt.Println("Exemple 2:", orangesRotting(grid2)) // Expected: -1
    
    grid3 := [][]int{{0, 2}}
    fmt.Println("Exemple 3:", orangesRotting(grid3)) // Expected: 0
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func orangesRotting(grid [][]int) int {
    rows, cols := len(grid), len(grid[0])
    queue := [][2]int{}
    freshOranges := 0
    
    // Étape 1: Trouver toutes les oranges pourries et compter les fraîches
    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            if grid[i][j] == 2 {
                queue = append(queue, [2]int{i, j})
            } else if grid[i][j] == 1 {
                freshOranges++
            }
        }
    }
    
    // Cas spécial: pas d'oranges fraîches
    if freshOranges == 0 {
        return 0
    }
    
    // Directions pour parcourir les 4 voisins
    directions := [][2]int{{-1, 0}, {1, 0}, {0, -1}, {0, 1}}
    minutes := 0
    
    // BFS niveau par niveau
    for len(queue) > 0 && freshOranges > 0 {
        size := len(queue)
        minutes++
        
        // Traiter toutes les oranges pourries du niveau actuel
        for i := 0; i < size; i++ {
            current := queue[0]
            queue = queue[1:]
            
            // Explorer les 4 directions
            for _, dir := range directions {
                newRow := current[0] + dir[0]
                newCol := current[1] + dir[1]
                
                // Vérifier les limites et si c'est une orange fraîche
                if newRow >= 0 && newRow < rows && 
                   newCol >= 0 && newCol < cols && 
                   grid[newRow][newCol] == 1 {
                    
                    // Faire pourrir l'orange
                    grid[newRow][newCol] = 2
                    freshOranges--
                    queue = append(queue, [2]int{newRow, newCol})
                }
            }
        }
    }
    
    // Si il reste des oranges fraîches, impossible de toutes les infecter
    if freshOranges > 0 {
        return -1
    }
    
    return minutes
}
```

**Simulation détaillée pour l'exemple 1 :**

```
Grille initiale: [[2,1,1],
                  [1,1,0],
                  [0,1,1]]

Initialisation:
- queue = [[0,0]] (position de l'orange pourrie)
- freshOranges = 6

Minute 1:
- Traiter (0,0): infecter (0,1) et (1,0)
- queue = [[0,1], [1,0]]
- freshOranges = 4
- Grille: [[2,2,1],
           [2,1,0],
           [0,1,1]]

Minute 2:  
- Traiter (0,1): infecter (0,2)
- Traiter (1,0): infecter (1,1)
- queue = [[0,2], [1,1]]
- freshOranges = 2
- Grille: [[2,2,2],
           [2,2,0],
           [0,1,1]]

Minute 3:
- Traiter (0,2): pas de nouveaux voisins
- Traiter (1,1): infecter (2,1)
- queue = [[2,1]]
- freshOranges = 1
- Grille: [[2,2,2],
           [2,2,0],
           [0,2,1]]

Minute 4:
- Traiter (2,1): infecter (2,2)
- queue = [[2,2]]
- freshOranges = 0
- Grille: [[2,2,2],
           [2,2,0],
           [0,2,2]]

Résultat: 4 minutes
```

**Points clés de l'algorithme :**

1. **Multi-source BFS** : Toutes les oranges pourries initiales sont ajoutées à la queue
2. **Traitement par niveaux** : `size = len(queue)` pour traiter une "minute" à la fois
3. **Comptage des fraîches** : Pour vérifier si toutes peuvent être infectées
4. **Modification in-place** : La grille sert aussi de tableau "visited"

**Complexité :**
- Temps : O(m × n) - chaque cellule est visitée au maximum une fois
- Espace : O(m × n) - dans le pire cas, toutes les cellules sont dans la queue

**Pourquoi BFS et pas DFS ?**
- On veut le **temps minimum** → BFS trouve le plus court chemin
- Propagation **simultanée** de toutes les oranges pourries → multi-source BFS
- **Niveau par niveau** correspond exactement aux minutes qui s'écoulent

</details>

## 🎯 Points clés à retenir

1. **Multi-source BFS** : Commencer avec plusieurs points de départ simultanément
2. **Traitement par niveaux** : Utiliser `size = len(queue)` pour séparer les minutes
3. **Comptage des cibles** : Suivre le nombre d'oranges fraîches pour détecter l'impossibilité
4. **Modification in-place** : Utiliser la grille elle-même comme tableau visited

## 🚀 Exercices similaires

- [01 Matrix](../medium/01-matrix.md) - Distance à la cellule 0 la plus proche
- [Walls and Gates](../medium/walls-gates.md) - Distance aux portes dans un donjon
- [Shortest Bridge](../hard/shortest-bridge.md) - Plus courte distance entre deux îles

## 🔍 Variations

**Si on voulait le chemin exact :**
```go
// Stocker aussi le parent pour reconstruire le chemin
type State struct {
    row, col int
    parent   *State
}
```

**Avec obstacles qui bougent :**
```go
// Ajouter une dimension temporelle à l'état
type State struct {
    row, col, time int
}
```

**Optimisation mémoire :**
```go
// Utiliser des directions plus compactes
var directions = [4][2]int{{-1,0}, {1,0}, {0,-1}, {0,1}}
```
