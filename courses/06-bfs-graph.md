# Cours 6 - BFS on Graphs Pattern

## 🎯 Quand utiliser ce pattern ?

Le **BFS (Breadth-First Search)** est essentiel pour :
- **Plus court chemin** dans un graphe non pondéré
- **Parcours niveau par niveau** (arbres, grilles)
- **Distance minimale** entre deux points
- **Connexité** et composantes connexes
- **Détection de cycles** dans les graphes non orientés

## 🔍 Comment identifier ce pattern

**Mots-clés dans l'énoncé :**
- "shortest path", "minimum steps"
- "level by level", "layer by layer"
- "minimum distance", "closest"
- "grid", "matrix", "2D array"
- "connected components"
- "word ladder", "minimum mutations"

**Signal principal :** Besoin de trouver le chemin le plus court ou explorer par niveaux.

## 🎯 BFS vs DFS - Quand utiliser quoi ?

| BFS | DFS |
|-----|-----|
| Plus court chemin | Tous les chemins |
| Exploration niveau par niveau | Exploration en profondeur |
| Queue (FIFO) | Stack/Récursion (LIFO) |
| Complexité espace O(largeur) | Complexité espace O(profondeur) |

## 📋 Template de base

### Template 1 - BFS Standard

```go
func bfs(start position) int {
    if start == target {
        return 0
    }
    
    queue := []position{start}
    visited := make(map[position]bool)
    visited[start] = true
    steps := 0
    
    for len(queue) > 0 {
        size := len(queue)
        steps++
        
        // Traiter tous les nœuds du niveau actuel
        for i := 0; i < size; i++ {
            current := queue[0]
            queue = queue[1:]
            
            // Explorer tous les voisins
            for _, neighbor := range getNeighbors(current) {
                if neighbor == target {
                    return steps
                }
                
                if !visited[neighbor] && isValid(neighbor) {
                    visited[neighbor] = true
                    queue = append(queue, neighbor)
                }
            }
        }
    }
    
    return -1 // Pas de chemin trouvé
}
```

### Template 2 - BFS sur grille 2D

```go
func bfsGrid(grid [][]int, start, end [2]int) int {
    if start == end {
        return 0
    }
    
    rows, cols := len(grid), len(grid[0])
    directions := [][2]int{{-1, 0}, {1, 0}, {0, -1}, {0, 1}}
    
    queue := [][2]int{start}
    visited := make([][]bool, rows)
    for i := range visited {
        visited[i] = make([]bool, cols)
    }
    visited[start[0]][start[1]] = true
    
    steps := 0
    
    for len(queue) > 0 {
        size := len(queue)
        steps++
        
        for i := 0; i < size; i++ {
            current := queue[0]
            queue = queue[1:]
            
            for _, dir := range directions {
                newRow := current[0] + dir[0]
                newCol := current[1] + dir[1]
                
                if newRow == end[0] && newCol == end[1] {
                    return steps
                }
                
                if newRow >= 0 && newRow < rows && 
                   newCol >= 0 && newCol < cols && 
                   !visited[newRow][newCol] && 
                   grid[newRow][newCol] != 1 { // 1 = obstacle
                    
                    visited[newRow][newCol] = true
                    queue = append(queue, [2]int{newRow, newCol})
                }
            }
        }
    }
    
    return -1
}
```

## 🚀 Exemple 1 - Number of Islands

**Problème :** Compter le nombre d'îles dans une grille binaire.

```go
func numIslands(grid [][]byte) int {
    if len(grid) == 0 || len(grid[0]) == 0 {
        return 0
    }
    
    rows, cols := len(grid), len(grid[0])
    islands := 0
    directions := [][2]int{{-1, 0}, {1, 0}, {0, -1}, {0, 1}}
    
    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            if grid[i][j] == '1' {
                islands++
                
                // BFS pour marquer toute l'île
                queue := [][2]int{{i, j}}
                grid[i][j] = '0' // Marquer comme visité
                
                for len(queue) > 0 {
                    current := queue[0]
                    queue = queue[1:]
                    
                    for _, dir := range directions {
                        newRow := current[0] + dir[0]
                        newCol := current[1] + dir[1]
                        
                        if newRow >= 0 && newRow < rows && 
                           newCol >= 0 && newCol < cols && 
                           grid[newRow][newCol] == '1' {
                            
                            grid[newRow][newCol] = '0'
                            queue = append(queue, [2]int{newRow, newCol})
                        }
                    }
                }
            }
        }
    }
    
    return islands
}
```

## 🚀 Exemple 2 - Word Ladder

**Problème :** Trouver la longueur de la plus courte transformation entre deux mots.

```go
func ladderLength(beginWord string, endWord string, wordList []string) int {
    // Créer un set pour recherche O(1)
    wordSet := make(map[string]bool)
    for _, word := range wordList {
        wordSet[word] = true
    }
    
    if !wordSet[endWord] {
        return 0
    }
    
    queue := []string{beginWord}
    visited := make(map[string]bool)
    visited[beginWord] = true
    length := 1
    
    for len(queue) > 0 {
        size := len(queue)
        length++
        
        for i := 0; i < size; i++ {
            current := queue[0]
            queue = queue[1:]
            
            // Générer tous les voisins possibles
            for _, neighbor := range getNeighbors(current, wordSet) {
                if neighbor == endWord {
                    return length
                }
                
                if !visited[neighbor] {
                    visited[neighbor] = true
                    queue = append(queue, neighbor)
                }
            }
        }
    }
    
    return 0
}

func getNeighbors(word string, wordSet map[string]bool) []string {
    neighbors := []string{}
    wordBytes := []byte(word)
    
    for i := 0; i < len(word); i++ {
        originalChar := wordBytes[i]
        
        for c := 'a'; c <= 'z'; c++ {
            if byte(c) == originalChar {
                continue
            }
            
            wordBytes[i] = byte(c)
            newWord := string(wordBytes)
            
            if wordSet[newWord] {
                neighbors = append(neighbors, newWord)
            }
        }
        
        wordBytes[i] = originalChar // Restaurer
    }
    
    return neighbors
}
```

## 🚀 Exemple 3 - Rotting Oranges

**Problème :** Temps minimum pour faire pourrir toutes les oranges.

```go
func orangesRotting(grid [][]int) int {
    rows, cols := len(grid), len(grid[0])
    queue := [][2]int{}
    freshOranges := 0
    
    // Trouver toutes les oranges pourries et compter les fraîches
    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            if grid[i][j] == 2 {
                queue = append(queue, [2]int{i, j})
            } else if grid[i][j] == 1 {
                freshOranges++
            }
        }
    }
    
    if freshOranges == 0 {
        return 0
    }
    
    directions := [][2]int{{-1, 0}, {1, 0}, {0, -1}, {0, 1}}
    minutes := 0
    
    for len(queue) > 0 && freshOranges > 0 {
        size := len(queue)
        minutes++
        
        for i := 0; i < size; i++ {
            current := queue[0]
            queue = queue[1:]
            
            for _, dir := range directions {
                newRow := current[0] + dir[0]
                newCol := current[1] + dir[1]
                
                if newRow >= 0 && newRow < rows && 
                   newCol >= 0 && newCol < cols && 
                   grid[newRow][newCol] == 1 {
                    
                    grid[newRow][newCol] = 2 // Pourrir l'orange
                    freshOranges--
                    queue = append(queue, [2]int{newRow, newCol})
                }
            }
        }
    }
    
    if freshOranges > 0 {
        return -1 // Impossible de pourrir toutes les oranges
    }
    
    return minutes
}
```

## 📊 Complexité

- **Temporelle :** O(V + E) où V = vertices, E = edges
  - Grille: O(m × n)
  - Graphe: O(nodes + edges)
- **Spatiale :** O(V) pour la queue et visited
  - Dans le pire cas, tous les nœuds peuvent être dans la queue

## ⚠️ Pièges courants

1. **Marquer comme visité** - Le faire au moment de l'ajout à la queue, pas du traitement
2. **Condition d'arrêt** - Vérifier si on a atteint la cible avant d'ajouter à la queue
3. **Traitement par niveaux** - Bien gérer `size = len(queue)` pour compter les étapes
4. **Gestion des cycles** - Toujours utiliser visited pour éviter les cycles infinis

## 🔄 Variations du pattern

### 1. BFS Multi-source
- Commencer avec plusieurs points de départ dans la queue
- Utile pour des problèmes comme "Rotting Oranges"

### 2. Bidirectional BFS
- BFS depuis le début et la fin simultanément
- Plus efficace pour de longs chemins

### 3. BFS avec states
- Chaque élément de la queue contient plus d'informations
- Ex: `{position, keys_collected, steps}`

## 🎭 Optimisations

### Queue efficace avec slice
```go
type Queue struct {
    items []interface{}
    front int
}

func (q *Queue) Enqueue(item interface{}) {
    q.items = append(q.items, item)
}

func (q *Queue) Dequeue() interface{} {
    if q.front >= len(q.items) {
        return nil
    }
    item := q.items[q.front]
    q.front++
    
    // Réinitialiser si nécessaire pour économiser la mémoire
    if q.front > len(q.items)/2 {
        q.items = q.items[q.front:]
        q.front = 0
    }
    
    return item
}
```

### Bidirectional BFS
```go
func bidirectionalBFS(start, end string, wordSet map[string]bool) int {
    if start == end {
        return 1
    }
    
    beginSet := map[string]bool{start: true}
    endSet := map[string]bool{end: true}
    visited := make(map[string]bool)
    steps := 1
    
    for len(beginSet) > 0 && len(endSet) > 0 {
        // Toujours explorer le plus petit set
        if len(beginSet) > len(endSet) {
            beginSet, endSet = endSet, beginSet
        }
        
        nextSet := make(map[string]bool)
        steps++
        
        for word := range beginSet {
            for _, neighbor := range getNeighbors(word, wordSet) {
                if endSet[neighbor] {
                    return steps
                }
                
                if !visited[neighbor] {
                    visited[neighbor] = true
                    nextSet[neighbor] = true
                }
            }
        }
        
        beginSet = nextSet
    }
    
    return 0
}
```

## 🏃‍♂️ Exercices pratiques

### Easy
- [Number of Islands](../exercises/bfs-graph/easy/num-islands.md)
- [Binary Tree Level Order Traversal](../exercises/bfs-graph/easy/level-order.md)

### Medium
- [Word Ladder](../exercises/bfs-graph/medium/word-ladder.md)
- [Rotting Oranges](../exercises/bfs-graph/medium/rotting-oranges.md)
- [01 Matrix](../exercises/bfs-graph/medium/01-matrix.md)
- [Open the Lock](../exercises/bfs-graph/medium/open-lock.md)

### Hard
- [Word Ladder II](../exercises/bfs-graph/hard/word-ladder-ii.md)
- [Sliding Puzzle](../exercises/bfs-graph/hard/min-window.md)

## 🧠 Stratégie de résolution

1. **Identifier si c'est du BFS** :
   - Plus court chemin ? → BFS
   - Tous les chemins ? → DFS
   - Niveau par niveau ? → BFS

2. **Définir l'état** :
   - Qu'est-ce qui représente une position ?
   - Quelles informations stocker dans la queue ?

3. **Implémenter getNeighbors** :
   - Comment passer d'un état au suivant ?
   - Vérifications de validité

4. **Gérer visited** :
   - Éviter de revisiter les mêmes états
   - Marquer au bon moment

5. **Compter les étapes** si nécessaire :
   - Traitement par niveaux avec `size = len(queue)`

## ➡️ Cours suivant

[Cours 7 - Backtracking Pattern](07-backtracking.md) - Pour l'exploration exhaustive avec retour en arrière.
