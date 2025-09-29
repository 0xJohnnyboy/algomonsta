# Exercice - Word Ladder II

**Niveau :** Hard
**Pattern :** BFS Graph
**Retour au cours :** [BFS on Graphs Pattern](../../courses/06-bfs-graph.md)

## 📝 Énoncé

Une **séquence de transformation** d'un mot `beginWord` à un mot `endWord` en utilisant un dictionnaire `wordList` est une séquence de mots `beginWord -> s1 -> s2 -> ... -> sk` telle que :

- Chaque paire de mots adjacents diffère d'exactement une lettre.
- Chaque `si` pour `1 <= i <= k` est dans `wordList`. Notez que `beginWord` n'a pas besoin d'être dans `wordList`.
- `sk == endWord`

Étant donné deux mots, `beginWord` et `endWord`, et un dictionnaire `wordList`, retournez **toutes les séquences de transformation les plus courtes** de `beginWord` à `endWord`, ou une liste vide si aucune séquence n'existe. Chaque séquence doit être retournée comme une liste de mots `[beginWord, s1, s2, ..., sk]`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: [["hit","hot","dot","dog","cog"],["hit","hot","lot","log","cog"]]
Explication: Il y a 2 chemins les plus courts :
"hit" -> "hot" -> "dot" -> "dog" -> "cog"
"hit" -> "hot" -> "lot" -> "log" -> "cog"
```

**Exemple 2 :**
```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
Output: []
Explication: Le endWord "cog" n'est pas dans wordList, donc aucune transformation n'est possible.
```

## 🎯 Contraintes

- `1 <= beginWord.length <= 5`
- `endWord.length == beginWord.length`
- `1 <= wordList.length <= 500`
- `wordList[i].length == beginWord.length`
- `beginWord`, `endWord`, et `wordList[i]` consistent uniquement en lettres anglaises minuscules.
- `beginWord != endWord`
- Tous les mots dans `wordList` sont **uniques**.

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez BFS pour trouver la distance la plus courte, puis DFS pour construire tous les chemins de cette longueur.

</details>

<details>
<summary>Indice 2</summary>

Construisez un graphe des parents pour chaque mot pour pouvoir reconstruire les chemins.

</details>

<details>
<summary>Indice 3</summary>

Arrêtez BFS dès que vous atteignez endWord pour garantir les chemins les plus courts.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func findLadders(beginWord string, endWord string, wordList []string) [][]string {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        beginWord string
        endWord   string
        wordList  []string
        expected  int // nombre de solutions attendues
    }{
        {
            "hit",
            "cog",
            []string{"hot", "dot", "dog", "lot", "log", "cog"},
            2,
        },
        {
            "hit",
            "cog",
            []string{"hot", "dot", "dog", "lot", "log"},
            0,
        },
        {
            "a",
            "c",
            []string{"a", "b", "c"},
            1,
        },
        {
            "red",
            "tax",
            []string{"ted", "tex", "red", "tax", "tad", "den", "rex", "pee"},
            1,
        },
    }

    fmt.Println("=== Tests Word Ladder II ===")
    for i, test := range tests {
        result := findLadders(test.beginWord, test.endWord, test.wordList)
        status := "✅"
        if len(result) != test.expected {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Begin: %s, End: %s\n", test.beginWord, test.endWord)
        fmt.Printf("  WordList: %v\n", test.wordList)
        fmt.Printf("  Expected paths: %d, Got: %d\n", test.expected, len(result))
        for j, path := range result {
            fmt.Printf("  Path %d: %v\n", j+1, path)
        }
        fmt.Println()
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func findLadders(beginWord string, endWord string, wordList []string) [][]string {
    // Créer un set des mots pour recherche O(1)
    wordSet := make(map[string]bool)
    for _, word := range wordList {
        wordSet[word] = true
    }

    if !wordSet[endWord] {
        return [][]string{}
    }

    // BFS pour trouver la distance minimale et construire le graphe des parents
    queue := []string{beginWord}
    visited := make(map[string]bool)
    visited[beginWord] = true
    parents := make(map[string][]string)
    found := false

    for len(queue) > 0 && !found {
        size := len(queue)
        levelVisited := make(map[string]bool)

        for i := 0; i < size; i++ {
            current := queue[0]
            queue = queue[1:]

            neighbors := getWordNeighbors(current, wordSet)
            for _, neighbor := range neighbors {
                if neighbor == endWord {
                    found = true
                }

                if !visited[neighbor] {
                    if !levelVisited[neighbor] {
                        levelVisited[neighbor] = true
                        queue = append(queue, neighbor)
                    }
                    parents[neighbor] = append(parents[neighbor], current)
                }
            }
        }

        // Marquer tous les mots du niveau comme visités
        for word := range levelVisited {
            visited[word] = true
        }
    }

    if !found {
        return [][]string{}
    }

    // DFS pour construire tous les chemins
    result := [][]string{}
    path := []string{endWord}
    buildPaths(endWord, beginWord, parents, path, &result)
    return result
}

func getWordNeighbors(word string, wordSet map[string]bool) []string {
    neighbors := []string{}
    bytes := []byte(word)

    for i := 0; i < len(bytes); i++ {
        originalChar := bytes[i]
        for c := 'a'; c <= 'z'; c++ {
            if c != rune(originalChar) {
                bytes[i] = byte(c)
                newWord := string(bytes)
                if wordSet[newWord] {
                    neighbors = append(neighbors, newWord)
                }
            }
        }
        bytes[i] = originalChar
    }

    return neighbors
}

func buildPaths(current, target string, parents map[string][]string, path []string, result *[][]string) {
    if current == target {
        // Inverser le chemin car on a construit de end vers begin
        reversed := make([]string, len(path))
        for i := 0; i < len(path); i++ {
            reversed[i] = path[len(path)-1-i]
        }
        *result = append(*result, reversed)
        return
    }

    for _, parent := range parents[current] {
        newPath := append([]string{parent}, path...)
        buildPaths(parent, target, parents, newPath, result)
    }
}
```

**Explication :**
1. **BFS niveau par niveau** : Trouve la distance minimale et construit le graphe des parents
2. **Gestion des niveaux** : Évite de marquer comme visité au milieu d'un niveau
3. **Graphe des parents** : Permet de reconstruire tous les chemins optimaux
4. **DFS de reconstruction** : Construit tous les chemins à partir du graphe des parents

**Complexité :**
- Temps : O(N × M² × 26) où N = nombre de mots, M = longueur des mots
- Espace : O(N × M) pour le graphe des parents et les chemins

</details>

## 🚀 Exercices similaires

- [Word Ladder](../medium/word-ladder.md)
- [Open the Lock](../medium/open-lock.md)
- [Sliding Puzzle](min-window.md)