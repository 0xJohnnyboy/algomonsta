# Exercice - Word Search II

**Niveau :** Hard  
**Pattern :** Trie + DFS (Advanced Patterns)  
**Retour au cours :** [Advanced Patterns](../../courses/09-advanced-patterns.md)

## 📝 Énoncé

Étant donné une grille 2D de lettres `board` et une liste de chaînes `words`, trouvez tous les mots de la liste qui peuvent être construits à partir des lettres de la grille.

Un mot peut être construit à partir de lettres de cellules adjacentes séquentiellement, où les cellules adjacentes sont voisines horizontalement ou verticalement. **La même cellule de lettre ne peut pas être utilisée plus d'une fois dans un mot.**

## 🔍 Exemples

**Exemple 1 :**
```
Input: 
board = [["o","a","a","n"],
         ["e","t","a","e"],
         ["i","h","k","r"],
         ["i","f","l","v"]]
words = ["oath","pea","eat","rain"]

Output: ["eat","oath"]

Explication:
- "oath": o(0,0) → a(0,1) → t(1,1) → h(2,1)
- "eat": e(1,0) → a(1,2) → t(1,1)
- "pea": Non trouvable dans la grille
- "rain": Non trouvable dans la grille
```

**Exemple 2 :**
```
Input:
board = [["a","b"],
         ["c","d"]]
words = ["abcb"]

Output: []

Explication: "abcb" ne peut pas être formé car cela nécessiterait d'utiliser 'b' deux fois.
```

## 🎯 Contraintes

- `m == board.length`
- `n == board[i].length`
- `1 <= m, n <= 12`
- `board[i][j]` est une lettre minuscule anglaise
- `1 <= words.length <= 3 * 10^4`
- `1 <= words[i].length <= 10`
- `words[i]` consiste en lettres minuscules anglaises
- Tous les `words[i]` sont uniques

## 💡 Indices

<details>
<summary>Indice 1</summary>

**Approche naïve :** Faire un DFS pour chaque mot depuis chaque cellule serait très inefficace. Il faut optimiser.

</details>

<details>
<summary>Indice 2</summary>

Utilisez une **Trie** pour stocker tous les mots. Cela permet de partager les préfixes communs et d'arrêter la recherche dès qu'aucun mot ne commence par le préfixe actuel.

</details>

<details>
<summary>Indice 3</summary>

Combinez **Trie + DFS** : Depuis chaque cellule, faites un DFS en suivant les chemins dans la Trie. Arrêtez dès que le préfixe n'existe plus dans la Trie.

</details>

<details>
<summary>Indice 4</summary>

**Optimisations importantes :**
- Marquez les cellules visitées pendant le DFS
- Supprimez les mots trouvés de la Trie pour éviter les doublons  
- Utilisez backtracking pour restaurer l'état

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

type TrieNode struct {
    children map[byte]*TrieNode
    word     string // Stocke le mot complet si c'est une fin de mot
}

type Trie struct {
    root *TrieNode
}

func NewTrie() *Trie {
    return &Trie{
        root: &TrieNode{
            children: make(map[byte]*TrieNode),
            word:     "",
        },
    }
}

func (t *Trie) Insert(word string) {
    // Votre implémentation ici
}

func findWords(board [][]byte, words []string) []string {
    // Votre code ici
    
}

func main() {
    // Test case 1
    board1 := [][]byte{
        {'o', 'a', 'a', 'n'},
        {'e', 't', 'a', 'e'},
        {'i', 'h', 'k', 'r'},
        {'i', 'f', 'l', 'v'},
    }
    words1 := []string{"oath", "pea", "eat", "rain"}
    fmt.Println("Test 1:", findWords(board1, words1)) // Expected: ["eat", "oath"]
    
    // Test case 2
    board2 := [][]byte{
        {'a', 'b'},
        {'c', 'd'},
    }
    words2 := []string{"abcb"}
    fmt.Println("Test 2:", findWords(board2, words2)) // Expected: []
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
type TrieNode struct {
    children map[byte]*TrieNode
    word     string // Stocke le mot complet si c'est une fin de mot
}

type Trie struct {
    root *TrieNode
}

func NewTrie() *Trie {
    return &Trie{
        root: &TrieNode{
            children: make(map[byte]*TrieNode),
            word:     "",
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
                word:     "",
            }
        }
        node = node.children[char]
    }
    
    node.word = word // Marquer la fin du mot
}

func findWords(board [][]byte, words []string) []string {
    // Construire la Trie avec tous les mots
    trie := NewTrie()
    for _, word := range words {
        trie.Insert(word)
    }
    
    rows, cols := len(board), len(board[0])
    result := []string{}
    
    // DFS depuis chaque cellule
    var dfs func(int, int, *TrieNode)
    dfs = func(row, col int, node *TrieNode) {
        // Vérifier les limites
        if row < 0 || row >= rows || col < 0 || col >= cols {
            return
        }
        
        char := board[row][col]
        
        // Si la cellule est déjà visitée ou le caractère n'existe pas dans la Trie
        if char == '#' || node.children[char] == nil {
            return
        }
        
        // Avancer dans la Trie
        node = node.children[char]
        
        // Si on a trouvé un mot complet
        if node.word != "" {
            result = append(result, node.word)
            node.word = "" // Éviter les doublons
        }
        
        // Marquer la cellule comme visitée
        board[row][col] = '#'
        
        // Explorer les 4 directions
        dfs(row+1, col, node)
        dfs(row-1, col, node)
        dfs(row, col+1, node)
        dfs(row, col-1, node)
        
        // Backtrack : restaurer la cellule
        board[row][col] = char
    }
    
    // Lancer DFS depuis chaque cellule
    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            dfs(i, j, trie.root)
        }
    }
    
    return result
}
```

**Version optimisée avec suppression de nœuds :**

```go
func findWordsOptimized(board [][]byte, words []string) []string {
    trie := NewTrie()
    for _, word := range words {
        trie.Insert(word)
    }
    
    rows, cols := len(board), len(board[0])
    result := []string{}
    
    var dfs func(int, int, *TrieNode, *TrieNode)
    dfs = func(row, col int, node, parent *TrieNode) {
        if row < 0 || row >= rows || col < 0 || col >= cols {
            return
        }
        
        char := board[row][col]
        if char == '#' || node.children[char] == nil {
            return
        }
        
        node = node.children[char]
        
        // Si on trouve un mot
        if node.word != "" {
            result = append(result, node.word)
            node.word = "" // Éviter les doublons
        }
        
        // Marquer comme visité
        board[row][col] = '#'
        
        // Explorer les 4 directions
        dfs(row+1, col, node, node)
        dfs(row-1, col, node, node)
        dfs(row, col+1, node, node)
        dfs(row, col-1, node, node)
        
        // Backtrack
        board[row][col] = char
        
        // Optimisation : supprimer les nœuds inutiles
        if len(node.children) == 0 && node.word == "" && parent != nil {
            delete(parent.children, char)
        }
    }
    
    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            dfs(i, j, trie.root, nil)
        }
    }
    
    return result
}
```

**Simulation pour l'exemple 1 :**

```
board = [["o","a","a","n"],
         ["e","t","a","e"],
         ["i","h","k","r"],
         ["i","f","l","v"]]
words = ["oath","pea","eat","rain"]

1. Construction de la Trie:
   root
   ├── o → a → t → h (word="oath")
   ├── p → e → a (word="pea")
   ├── e → a → t (word="eat")
   └── r → a → i → n (word="rain")

2. DFS depuis (0,0) = 'o':
   - Suivre o → a(0,1) → t(1,1) → h(2,1)
   - Mot "oath" trouvé ! Ajouter au résultat
   
3. DFS depuis (1,0) = 'e':
   - Suivre e → a(1,2) → t(1,1)
   - Mot "eat" trouvé ! Ajouter au résultat
   
4. Autres positions:
   - Aucun chemin pour "pea" ou "rain" trouvé
   
Résultat: ["oath", "eat"]
```

**Explication de l'algorithme :**

1. **Construction de la Trie :** O(M × L) où M = nombre de mots, L = longueur moyenne
   - Tous les mots sont insérés dans la Trie
   - Les préfixes communs sont partagés

2. **DFS avec Trie :** O(N × M × 4^L) dans le pire cas
   - Pour chaque cellule, on fait un DFS
   - La Trie permet d'arrêter tôt si aucun mot ne commence par le préfixe actuel
   - Backtracking pour explorer toutes les possibilités

3. **Optimisations clés :**
   - **Partage de préfixes** : La Trie évite de refaire les mêmes vérifications
   - **Arrêt précoce** : Si le préfixe n'existe pas dans la Trie, on arrête
   - **Suppression de mots** : `node.word = ""` évite les doublons
   - **Nettoyage** : Supprimer les nœuds inutiles (version optimisée)

**Complexité :**
- **Construction Trie :** O(M × L) où M = nombre de mots, L = longueur moyenne
- **DFS :** O(N × M × 4^L) où N = cellules de la grille
- **Espace :** O(M × L) pour la Trie

**Points clés de l'implémentation :**

1. **Trie avec mots complets :** Stocker `word` dans le nœud final évite de reconstruire
2. **Backtracking :** Marquer/démarquer les cellules visitées
3. **Éviter les doublons :** Vider `node.word` après l'avoir trouvé
4. **Optimisation :** Supprimer les nœuds vides pour améliorer les performances

**Pourquoi Trie + DFS est optimal :**

1. **Versus DFS naïf :** Au lieu de faire DFS pour chaque mot séparément, on fait un seul DFS qui peut trouver plusieurs mots
2. **Partage de préfixes :** Si "eat" et "ear" sont dans la liste, on partage "ea"
3. **Arrêt précoce :** Si aucun mot ne commence par "xyz", on arrête immédiatement
4. **Éviter les répétitions :** La Trie structure naturellement la recherche

</details>

## 🎯 Points clés à retenir

1. **Combinaison de patterns** : Trie pour l'efficacité + DFS pour l'exploration
2. **Optimisation critique** : Partage des préfixes via la Trie
3. **Backtracking essentiel** : Marquer/démarquer les cellules visitées
4. **Gestion des doublons** : Vider les mots trouvés

## 🚀 Exercices similaires

- [Word Search I](../medium/word-search-i.md) - Version simple avec un seul mot
- [Implement Trie](../medium/implement-trie.md) - Structure de données de base
- [Add and Search Word](../medium/add-search-word.md) - Trie avec wildcards

## 🔍 Variations

**Word Search avec wildcards :**
```go
// Ajouter support pour '.' qui match n'importe quel caractère
func searchWithWildcard(board [][]byte, word string) bool {
    // Modifier le DFS pour gérer les '.'
}
```

**Stream de mots :**
```go
// Ajouter/supprimer des mots dynamiquement
type WordSearchStream struct {
    trie *Trie
    board [][]byte
}

func (ws *WordSearchStream) AddWord(word string) {
    ws.trie.Insert(word)
}

func (ws *WordSearchStream) Search() []string {
    // Même logique que findWords
}
```

## 🎓 Concepts Advanced Patterns illustrés

- ✅ **Combinaison de patterns** - Trie + DFS travaillent ensemble
- ✅ **Optimisation de complexité** - De O(M×N×4^L) à O(M×L + N×4^L)
- ✅ **Structure de données spécialisée** - Trie pour les préfixes
- ✅ **Backtracking avancé** - État complexe avec Trie
- ✅ **Memory management** - Suppression de nœuds inutiles

## 💡 Leçons pour les Advanced Patterns

1. **Identifier les inefficacités** - DFS naïf répète beaucoup de travail
2. **Choisir la bonne structure** - Trie naturelle pour les préfixes
3. **Combiner intelligemment** - Trie guide le DFS, pas l'inverse
4. **Optimiser progressivement** - Version basique → optimisations
5. **Gérer la complexité** - Plus de code mais meilleure performance

Ce problème illustre parfaitement comment les Advanced Patterns permettent de résoudre des problèmes qui seraient impraticables avec des approches naïves !
