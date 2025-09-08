# Cours 9 - Advanced Patterns

## 🎯 Qu'est-ce que les Advanced Patterns ?

Les **Advanced Patterns** combinent plusieurs techniques algorithmiques ou utilisent des approches spécialisées pour résoudre des problèmes complexes. Ils nécessitent une maîtrise solide des patterns de base.

## 🔍 Quand utiliser ces patterns ?

- **Problèmes multi-contraintes** nécessitant plusieurs techniques
- **Optimisations avancées** avec structures de données spécialisées
- **Problèmes de concours** et entretiens de niveau senior
- **Cas d'usage réels** complexes en industrie

## 📋 Patterns couverts dans ce cours

### 1. 🎯 **Union Find (Disjoint Set)**
### 2. 🌊 **Topological Sort**
### 3. 🎭 **Trie (Prefix Tree)**
### 4. 🔢 **Bit Manipulation Avancé**
### 5. 🎲 **Reservoir Sampling**
### 6. 🏔️ **Sweep Line Algorithm**
### 7. 🔄 **Cycle Detection Avancé**

---

## 1. 🎯 Union Find (Disjoint Set)

### Quand l'utiliser
- **Connectivité dynamique** dans les graphes
- **Groupement d'éléments** par équivalence
- **Détection de cycles** dans les graphes non orientés
- **Problèmes de percolation**

### Template de base
```go
type UnionFind struct {
    parent []int
    rank   []int
    count  int
}

func NewUnionFind(n int) *UnionFind {
    parent := make([]int, n)
    rank := make([]int, n)
    
    for i := 0; i < n; i++ {
        parent[i] = i
        rank[i] = 0
    }
    
    return &UnionFind{
        parent: parent,
        rank:   rank,
        count:  n,
    }
}

func (uf *UnionFind) Find(x int) int {
    if uf.parent[x] != x {
        uf.parent[x] = uf.Find(uf.parent[x]) // Path compression
    }
    return uf.parent[x]
}

func (uf *UnionFind) Union(x, y int) bool {
    rootX := uf.Find(x)
    rootY := uf.Find(y)
    
    if rootX == rootY {
        return false // Déjà connectés
    }
    
    // Union by rank
    if uf.rank[rootX] < uf.rank[rootY] {
        uf.parent[rootX] = rootY
    } else if uf.rank[rootX] > uf.rank[rootY] {
        uf.parent[rootY] = rootX
    } else {
        uf.parent[rootY] = rootX
        uf.rank[rootX]++
    }
    
    uf.count--
    return true
}

func (uf *UnionFind) Connected(x, y int) bool {
    return uf.Find(x) == uf.Find(y)
}

func (uf *UnionFind) Count() int {
    return uf.count
}
```

### Exemple - Number of Islands II (Dynamic)
```go
func numIslands2(m, n int, positions [][]int) []int {
    uf := NewUnionFind(m * n)
    grid := make([][]bool, m)
    for i := range grid {
        grid[i] = make([]bool, n)
    }
    
    directions := [][2]int{{-1, 0}, {1, 0}, {0, -1}, {0, 1}}
    result := []int{}
    islands := 0
    
    for _, pos := range positions {
        row, col := pos[0], pos[1]
        
        if grid[row][col] {
            result = append(result, islands)
            continue
        }
        
        grid[row][col] = true
        islands++
        
        index := row*n + col
        
        // Connecter aux îles adjacentes
        for _, dir := range directions {
            newRow, newCol := row+dir[0], col+dir[1]
            
            if newRow >= 0 && newRow < m && newCol >= 0 && newCol < n && grid[newRow][newCol] {
                newIndex := newRow*n + newCol
                if uf.Union(index, newIndex) {
                    islands--
                }
            }
        }
        
        result = append(result, islands)
    }
    
    return result
}
```

---

## 2. 🌊 Topological Sort

### Quand l'utiliser
- **Graphes acycliques dirigés** (DAG)
- **Ordonnancement de tâches** avec dépendances
- **Détection de cycles** dans les graphes dirigés
- **Course Schedule** problems

### Template - Kahn's Algorithm
```go
func topologicalSort(numNodes int, edges [][]int) []int {
    // Construire le graphe et calculer les indegrees
    graph := make([][]int, numNodes)
    indegree := make([]int, numNodes)
    
    for _, edge := range edges {
        from, to := edge[0], edge[1]
        graph[from] = append(graph[from], to)
        indegree[to]++
    }
    
    // Queue avec tous les nœuds d'indegree 0
    queue := []int{}
    for i := 0; i < numNodes; i++ {
        if indegree[i] == 0 {
            queue = append(queue, i)
        }
    }
    
    result := []int{}
    
    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:]
        result = append(result, node)
        
        // Réduire l'indegree des voisins
        for _, neighbor := range graph[node] {
            indegree[neighbor]--
            if indegree[neighbor] == 0 {
                queue = append(queue, neighbor)
            }
        }
    }
    
    // Si tous les nœuds ne sont pas traités, il y a un cycle
    if len(result) != numNodes {
        return []int{} // Cycle détecté
    }
    
    return result
}
```

### Exemple - Course Schedule II
```go
func findOrder(numCourses int, prerequisites [][]int) []int {
    return topologicalSort(numCourses, prerequisites)
}
```

---

## 3. 🎭 Trie (Prefix Tree)

### Quand l'utiliser
- **Recherche de préfixes** efficace
- **Auto-complétion** et suggestions
- **Word search** dans une grille
- **Longest prefix matching**

### Template de base
```go
type TrieNode struct {
    children map[byte]*TrieNode
    isEnd    bool
}

type Trie struct {
    root *TrieNode
}

func NewTrie() *Trie {
    return &Trie{
        root: &TrieNode{
            children: make(map[byte]*TrieNode),
            isEnd:    false,
        },
    }
}

func (t *Trie) Insert(word string) {
    node := t.root
    
    for i := 0; i < len(word); i++ {
        char := word[i]
        if _, exists := node.children[char]; !exists {
            node.children[char] = &TrieNode{
                children: make(map[byte]*TrieNode),
                isEnd:    false,
            }
        }
        node = node.children[char]
    }
    
    node.isEnd = true
}

func (t *Trie) Search(word string) bool {
    node := t.root
    
    for i := 0; i < len(word); i++ {
        char := word[i]
        if _, exists := node.children[char]; !exists {
            return false
        }
        node = node.children[char]
    }
    
    return node.isEnd
}

func (t *Trie) StartsWith(prefix string) bool {
    node := t.root
    
    for i := 0; i < len(prefix); i++ {
        char := prefix[i]
        if _, exists := node.children[char]; !exists {
            return false
        }
        node = node.children[char]
    }
    
    return true
}
```

### Exemple - Word Search II
```go
func findWords(board [][]byte, words []string) []string {
    trie := NewTrie()
    for _, word := range words {
        trie.Insert(word)
    }
    
    rows, cols := len(board), len(board[0])
    result := []string{}
    
    var dfs func(int, int, *TrieNode, string)
    dfs = func(row, col int, node *TrieNode, path string) {
        if row < 0 || row >= rows || col < 0 || col >= cols {
            return
        }
        
        char := board[row][col]
        if char == '#' { // Déjà visité
            return
        }
        
        if _, exists := node.children[char]; !exists {
            return
        }
        
        node = node.children[char]
        path += string(char)
        
        if node.isEnd {
            result = append(result, path)
            node.isEnd = false // Éviter les doublons
        }
        
        // Marquer comme visité
        board[row][col] = '#'
        
        // Explorer les 4 directions
        dfs(row+1, col, node, path)
        dfs(row-1, col, node, path)
        dfs(row, col+1, node, path)
        dfs(row, col-1, node, path)
        
        // Backtrack
        board[row][col] = char
    }
    
    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            dfs(i, j, trie.root, "")
        }
    }
    
    return result
}
```

---

## 4. 🔢 Bit Manipulation Avancé

### Techniques essentielles
```go
// Opérations de base
func setBit(num, pos int) int    { return num | (1 << pos) }
func clearBit(num, pos int) int  { return num & ^(1 << pos) }
func toggleBit(num, pos int) int { return num ^ (1 << pos) }
func checkBit(num, pos int) bool { return (num & (1 << pos)) != 0 }

// Techniques avancées
func rightmostSetBit(num int) int     { return num & (-num) }
func countSetBits(num int) int        { /* Brian Kernighan's algorithm */ }
func isPowerOfTwo(num int) bool       { return num > 0 && (num & (num-1)) == 0 }
func nextPowerOfTwo(num int) int      { /* Implementation */ }
```

### Exemple - Single Number III
```go
func singleNumber(nums []int) []int {
    // XOR tous les nombres
    xor := 0
    for _, num := range nums {
        xor ^= num
    }
    
    // Trouver le bit le plus à droite qui diffère
    diff := xor & (-xor)
    
    // Séparer en deux groupes
    group1, group2 := 0, 0
    for _, num := range nums {
        if (num & diff) == 0 {
            group1 ^= num
        } else {
            group2 ^= num
        }
    }
    
    return []int{group1, group2}
}
```

---

## 5. 🎲 Reservoir Sampling

### Quand l'utiliser
- **Échantillonnage aléatoire** d'un stream
- **Taille inconnue** des données d'entrée
- **Mémoire limitée** pour l'échantillonnage

### Template
```go
import "math/rand"

type ReservoirSampler struct {
    reservoir []int
    k         int
    count     int
}

func NewReservoirSampler(k int) *ReservoirSampler {
    return &ReservoirSampler{
        reservoir: make([]int, 0, k),
        k:         k,
        count:     0,
    }
}

func (rs *ReservoirSampler) Add(value int) {
    rs.count++
    
    if len(rs.reservoir) < rs.k {
        rs.reservoir = append(rs.reservoir, value)
    } else {
        // Remplacer avec probabilité k/count
        j := rand.Intn(rs.count)
        if j < rs.k {
            rs.reservoir[j] = value
        }
    }
}

func (rs *ReservoirSampler) GetSample() []int {
    result := make([]int, len(rs.reservoir))
    copy(result, rs.reservoir)
    return result
}
```

---

## 6. 🏔️ Sweep Line Algorithm

### Quand l'utiliser
- **Intervalles qui se chevauchent**
- **Événements temporels**
- **Problèmes géométriques** 2D

### Template - Merge Intervals
```go
import "sort"

func mergeIntervals(intervals [][]int) [][]int {
    if len(intervals) <= 1 {
        return intervals
    }
    
    // Trier par début d'intervalle
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][0] < intervals[j][0]
    })
    
    result := [][]int{intervals[0]}
    
    for i := 1; i < len(intervals); i++ {
        current := intervals[i]
        last := result[len(result)-1]
        
        if current[0] <= last[1] {
            // Chevaucher : fusionner
            last[1] = max(last[1], current[1])
        } else {
            // Pas de chevauchement
            result = append(result, current)
        }
    }
    
    return result
}
```

### Exemple - Meeting Rooms II
```go
import "container/heap"

func minMeetingRooms(intervals [][]int) int {
    if len(intervals) == 0 {
        return 0
    }
    
    // Trier par heure de début
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][0] < intervals[j][0]
    })
    
    // Min heap pour les heures de fin
    endTimes := &IntHeap{}
    heap.Init(endTimes)
    
    maxRooms := 0
    
    for _, interval := range intervals {
        start, end := interval[0], interval[1]
        
        // Libérer les salles terminées
        for endTimes.Len() > 0 && (*endTimes)[0] <= start {
            heap.Pop(endTimes)
        }
        
        // Ajouter la nouvelle réunion
        heap.Push(endTimes, end)
        
        maxRooms = max(maxRooms, endTimes.Len())
    }
    
    return maxRooms
}

type IntHeap []int
func (h IntHeap) Len() int           { return len(h) }
func (h IntHeap) Less(i, j int) bool { return h[i] < h[j] }
func (h IntHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *IntHeap) Push(x interface{}) { *h = append(*h, x.(int)) }
func (h *IntHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[0 : n-1]
    return x
}
```

---

## 7. 🔄 Cycle Detection Avancé

### Floyd's Cycle Detection (Tortoise and Hare)
```go
type ListNode struct {
    Val  int
    Next *ListNode
}

func hasCycle(head *ListNode) bool {
    if head == nil || head.Next == nil {
        return false
    }
    
    slow, fast := head, head
    
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        
        if slow == fast {
            return true
        }
    }
    
    return false
}

func detectCycle(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return nil
    }
    
    // Phase 1: Détecter le cycle
    slow, fast := head, head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        if slow == fast {
            break
        }
    }
    
    if fast == nil || fast.Next == nil {
        return nil // Pas de cycle
    }
    
    // Phase 2: Trouver le début du cycle
    slow = head
    for slow != fast {
        slow = slow.Next
        fast = fast.Next
    }
    
    return slow
}
```

## 📊 Complexité des Advanced Patterns

| Pattern | Temps | Espace | Usage |
|---------|-------|--------|-------|
| Union Find | O(α(n)) | O(n) | Connectivité dynamique |
| Topological Sort | O(V + E) | O(V) | Ordonnancement |
| Trie | O(m) | O(ALPHABET_SIZE × N × M) | Recherche de mots |
| Bit Manipulation | O(1) | O(1) | Optimisations |
| Reservoir Sampling | O(1) per item | O(k) | Échantillonnage |
| Sweep Line | O(n log n) | O(n) | Intervalles |
| Cycle Detection | O(n) | O(1) | Détection de cycles |

## 🏃‍♂️ Exercices pratiques

<!-- ### Easy/Medium -->
<!-- - [Union Find - Number of Islands](../exercises/advanced-patterns/medium/union-find-islands.md) -->
<!-- - [Trie - Implement Trie](../exercises/advanced-patterns/medium/implement-trie.md) -->
<!-- - [Bit Manipulation - Single Number](../exercises/advanced-patterns/easy/single-number.md) -->

### Hard
<!-- - [Topological Sort - Alien Dictionary](../exercises/advanced-patterns/hard/alien-dictionary.md) -->
<!-- - [Sweep Line - Meeting Rooms II](../exercises/advanced-patterns/hard/meeting-rooms-ii.md) -->
- [Trie + DFS - Word Search II](../exercises/advanced-patterns/hard/word-search-ii.md)

## 🧠 Quand combiner les patterns

### Union Find + DFS
```go
// Pour les problèmes de connectivité avec exploration
func solveWithUnionFindAndDFS(grid [][]int) {
    // Utiliser Union Find pour grouper
    // Puis DFS pour explorer chaque composante
}
```

### Trie + Backtracking
```go
// Pour Word Search avec multiples mots
func wordSearchWithTrie(board [][]byte, words []string) []string {
    // Construire Trie avec tous les mots
    // Utiliser backtracking pour explorer
}
```

### Binary Search + Greedy
```go
// Pour optimiser une fonction monotone
func binarySearchWithGreedy(arr []int, target int) int {
    // Binary search sur l'espace de solution
    // Greedy pour valider chaque candidat
}
```

## 💡 Conseils pour maîtriser les Advanced Patterns

### ✅ Stratégie d'apprentissage
1. **Maîtriser les bases** avant de passer aux patterns avancés
2. **Comprendre quand** utiliser chaque pattern
3. **Pratiquer les combinaisons** de patterns
4. **Analyser la complexité** de vos solutions

### 🎯 En entretien
1. **Identifier** si le problème nécessite un pattern avancé
2. **Expliquer** pourquoi vous choisissez ce pattern
3. **Implémenter** progressivement
4. **Tester** avec des cas complexes

### 📚 Pour approfondir
- **Competitive Programming** - Codeforces, AtCoder
- **System Design** - Cas d'usage réels
- **Research Papers** - Algorithmes de pointe
- **Open Source** - Étudier les implémentations

## ✨ Conclusion

Les Advanced Patterns représentent l'étape finale de votre formation algorithmique. Ils nécessitent :

1. **Solide maîtrise** des patterns de base
2. **Capacité d'analyse** pour identifier le bon pattern
3. **Pratique intensive** sur des problèmes complexes
4. **Compréhension** de quand combiner plusieurs techniques

**Félicitations ! 🎉** Vous avez maintenant une boîte à outils complète pour résoudre pratiquement n'importe quel problème algorithmique.
