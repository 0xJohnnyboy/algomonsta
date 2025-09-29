# Exercice - Word Ladder

**Niveau :** Medium
**Pattern :** BFS Plus court chemin
**Retour au cours :** [BFS on Graphs Pattern](../../courses/06-bfs-graph.md)

## 📝 Énoncé

Un **transformation sequence** de `beginWord` à `endWord` en utilisant un dictionnaire `wordList` est une séquence de mots `beginWord -> s1 -> s2 -> ... -> sk` telle que :

- Chaque `si` adjacent diffère exactement d'une lettre de `si-1`
- Chaque `si` est dans `wordList`. Notez que `beginWord` n'a pas besoin d'être dans `wordList`
- `sk == endWord`

Étant donné `beginWord`, `endWord`, et un dictionnaire `wordList`, retournez la **longueur de la plus courte transformation sequence** de `beginWord` à `endWord`, ou `0` si une telle transformation n'existe pas.

## 🔍 Exemples

**Exemple 1 :**
```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5
Explication: Une des plus courtes transformations est "hit" -> "hot" -> "dot" -> "dog" -> "cog", longueur = 5.
```

**Exemple 2 :**
```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
Output: 0
Explication: endWord "cog" n'est pas dans wordList, donc aucune transformation possible.
```

**Exemple 3 :**
```
Input: beginWord = "a", endWord = "c", wordList = ["a","b","c"]
Output: 2
Explication: "a" -> "c", longueur = 2.
```

**Exemple 4 :**
```
Input: beginWord = "hot", endWord = "dog", wordList = ["hot","dog"]
Output: 0
Explication: Il n'y a pas de transformation possible car "hot" et "dog" diffèrent de plus d'une lettre.
```

## 🎯 Contraintes

- `1 <= beginWord.length <= 10`
- `endWord.length == beginWord.length`
- `1 <= wordList.length <= 5000`
- `wordList[i].length == beginWord.length`
- `beginWord`, `endWord`, et `wordList[i]` consistent seulement en lettres minuscules anglaises
- `beginWord != endWord`
- Tous les mots dans `wordList` sont **uniques**

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème de **plus court chemin** dans un graphe non pondéré. Chaque mot est un nœud, et il y a une arête entre deux mots s'ils diffèrent d'exactement une lettre.

</details>

<details>
<summary>Indice 2</summary>

Utilisez **BFS** pour trouver le plus court chemin. Commencez par `beginWord` et explorez tous les mots accessibles niveau par niveau.

</details>

<details>
<summary>Indice 3</summary>

Pour chaque mot, générez toutes les transformations possibles en changeant une lettre à la fois (a-z). Vérifiez si le mot transformé existe dans `wordList`.

</details>

<details>
<summary>Indice 4</summary>

Utilisez un set/map pour `wordList` pour une recherche O(1), et gardez trace des mots visités pour éviter les cycles.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func ladderLength(beginWord string, endWord string, wordList []string) int {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println("Exemple 1:", ladderLength("hit", "cog", []string{"hot","dot","dog","lot","log","cog"})) // Expected: 5

    fmt.Println("Exemple 2:", ladderLength("hit", "cog", []string{"hot","dot","dog","lot","log"})) // Expected: 0

    fmt.Println("Exemple 3:", ladderLength("a", "c", []string{"a","b","c"})) // Expected: 2

    fmt.Println("Exemple 4:", ladderLength("hot", "dog", []string{"hot","dog"})) // Expected: 0

    fmt.Println("Exemple 5:", ladderLength("cat", "dog", []string{"bat","bag","dag","dot","dog"})) // Expected: 5

    fmt.Println("Exemple 6:", ladderLength("lost", "miss", []string{"most","mist","miss","lost","fist","fish"})) // Expected: 4

    fmt.Println("Exemple 7:", ladderLength("red", "tax", []string{"ted","tex","red","tax","tad","den","rex","pee"})) // Expected: 4

    fmt.Println("Exemple 8:", ladderLength("talk", "tail", []string{"talk","tons","fall","tail","gale","hall","negs"})) // Expected: 0
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func ladderLength(beginWord string, endWord string, wordList []string) int {
    // Vérifier si endWord est dans wordList
    wordSet := make(map[string]bool)
    for _, word := range wordList {
        wordSet[word] = true
    }

    if !wordSet[endWord] {
        return 0
    }

    // BFS
    queue := []string{beginWord}
    visited := make(map[string]bool)
    visited[beginWord] = true
    level := 1

    for len(queue) > 0 {
        size := len(queue)

        // Traiter tous les mots du niveau actuel
        for i := 0; i < size; i++ {
            currentWord := queue[0]
            queue = queue[1:]

            // Si on a atteint le mot cible
            if currentWord == endWord {
                return level
            }

            // Générer toutes les transformations possibles
            for j := 0; j < len(currentWord); j++ {
                for c := byte('a'); c <= byte('z'); c++ {
                    if c == currentWord[j] {
                        continue // Pas de changement
                    }

                    // Créer le nouveau mot
                    newWord := currentWord[:j] + string(c) + currentWord[j+1:]

                    // Vérifier si le nouveau mot est valide et non visité
                    if wordSet[newWord] && !visited[newWord] {
                        visited[newWord] = true
                        queue = append(queue, newWord)
                    }
                }
            }
        }

        level++
    }

    return 0 // Aucun chemin trouvé
}
```

**Simulation détaillée pour l'exemple 1 :**

```
beginWord = "hit", endWord = "cog"
wordList = ["hot","dot","dog","lot","log","cog"]

Initialisation:
- wordSet = {"hot": true, "dot": true, "dog": true, "lot": true, "log": true, "cog": true}
- queue = ["hit"]
- visited = {"hit": true}
- level = 1

Niveau 1 (level = 1):
- size = 1
- currentWord = "hit"
- Générer transformations de "hit":
  - Position 0: "ait", "bit", ..., "git", ..., "wit" (aucun dans wordSet)
  - Position 1: "hat", "hbt", ..., "hot" ✓ (dans wordSet, non visité)
  - Position 2: "hia", "hib", ..., "his" (aucun dans wordSet)
- Ajouter "hot": queue = ["hot"], visited = {"hit": true, "hot": true}
- level = 2

Niveau 2 (level = 2):
- size = 1
- currentWord = "hot"
- Générer transformations de "hot":
  - Position 0: "aot", "bot", "cot", "dot" ✓, ..., "lot" ✓, ...
  - Position 1: "hat", "hbt", ..., "hit" (déjà visité)
  - Position 2: "hoa", "hob", "hoc", ..., "hoz" (aucun valide)
- Ajouter "dot", "lot": queue = ["dot", "lot"]
- visited = {"hit": true, "hot": true, "dot": true, "lot": true}
- level = 3

Niveau 3 (level = 3):
- size = 2
- currentWord = "dot":
  - Transformations: "dog" ✓ (nouveau)
- currentWord = "lot":
  - Transformations: "log" ✓ (nouveau)
- queue = ["dog", "log"]
- visited = {"hit": true, "hot": true, "dot": true, "lot": true, "dog": true, "log": true}
- level = 4

Niveau 4 (level = 4):
- size = 2
- currentWord = "dog":
  - Transformations: "cog" ✓ (nouveau)
- currentWord = "log":
  - Transformations: "cog" (mais déjà ajouté dans cette itération)
- queue = ["cog"]
- level = 5

Niveau 5 (level = 5):
- size = 1
- currentWord = "cog"
- currentWord == endWord → retourner level = 5

Résultat: 5 (chemin = "hit" → "hot" → "dot" → "dog" → "cog")
```

**Points clés de l'algorithme :**

1. **Graphe implicite** : Chaque mot = nœud, arête si différence d'une lettre
2. **BFS niveau par niveau** : Garantit le plus court chemin
3. **Génération des voisins** : Changer chaque position avec toutes les lettres
4. **Optimisation avec set** : Recherche O(1) dans wordList

**Complexité :**
- Temps : O(M² × N) où M = longueur des mots, N = taille de wordList
  - Pour chaque mot, on génère M × 26 transformations possibles
  - Chaque transformation nécessite O(M) pour créer le string
- Espace : O(M × N) pour stocker wordSet et visited

**Pourquoi BFS ?**
- **Plus court chemin** : BFS trouve le chemin avec le minimum d'étapes
- **Niveau par niveau** : Chaque niveau représente une transformation de plus
- **Optimal** : Premier chemin trouvé = plus court chemin garanti

</details>

## 🎯 Points clés à retenir

1. **Modélisation en graphe** : Mots = nœuds, transformations = arêtes
2. **BFS pour plus court chemin** : Exploration niveau par niveau garantit l'optimalité
3. **Génération des voisins** : Changer chaque position avec toutes les lettres possibles
4. **Optimisation avec structures de données** : Set/Map pour recherches O(1)

## 🚀 Exercices similaires

- [Word Ladder II](../hard/word-ladder-ii.md) - Trouver tous les plus courts chemins
- [Minimum Genetic Mutation](../medium/genetic-mutation.md) - Même pattern avec mutations génétiques
- [Open the Lock](../medium/open-lock.md) - Transformation de combinaisons

## 🔍 Variations

**Optimisation bidirectionnelle BFS :**
```go
func ladderLengthBidirectional(beginWord string, endWord string, wordList []string) int {
    wordSet := make(map[string]bool)
    for _, word := range wordList {
        wordSet[word] = true
    }

    if !wordSet[endWord] {
        return 0
    }

    // Deux ensembles pour BFS bidirectionnelle
    beginSet := map[string]bool{beginWord: true}
    endSet := map[string]bool{endWord: true}
    visited := make(map[string]bool)
    level := 1

    for len(beginSet) > 0 && len(endSet) > 0 {
        // Toujours explorer depuis le plus petit ensemble
        if len(beginSet) > len(endSet) {
            beginSet, endSet = endSet, beginSet
        }

        nextSet := make(map[string]bool)
        for word := range beginSet {
            // Génération des transformations...
        }

        beginSet = nextSet
        level++
    }

    return 0
}
```

**Pré-traitement avec patterns :**
```go
// Créer un mapping pattern -> mots
// Exemple: "h*t" -> ["hot", "hit", "hat"]
patterns := make(map[string][]string)
for _, word := range wordList {
    for i := 0; i < len(word); i++ {
        pattern := word[:i] + "*" + word[i+1:]
        patterns[pattern] = append(patterns[pattern], word)
    }
}
```

**Version avec A* (heuristique) :**
```go
type State struct {
    word     string
    level    int
    priority int // level + heuristique
}

// Heuristique: nombre de lettres différentes avec endWord
func heuristic(word, target string) int {
    diff := 0
    for i := 0; i < len(word); i++ {
        if word[i] != target[i] {
            diff++
        }
    }
    return diff
}
```