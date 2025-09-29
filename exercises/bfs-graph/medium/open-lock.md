# Exercice - Open the Lock

**Niveau :** Medium
**Pattern :** BFS Graph
**Retour au cours :** [BFS on Graphs Pattern](../../courses/06-bfs-graph.md)

## 📝 Énoncé

Vous avez un cadenas avec 4 molettes circulaires. Chaque molette a 10 slots : `'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'`. Les molettes peuvent tourner librement et s'enroulent : par exemple, on peut passer de `'9'` à `'0'` et de `'0'` à `'9'`. Chaque mouvement consiste à tourner une molette d'un slot.

Le cadenas commence initialement à `'0000'`, une chaîne représentant l'état des 4 molettes.

Vous avez une liste de `deadends` morts-vivants, ce qui signifie que si le cadenas affiche l'un de ces codes, les molettes du cadenas cesseront de tourner et vous ne pourrez pas l'ouvrir.

Étant donné un `target` représentant la valeur des molettes qui déverrouilleront le cadenas, retournez le nombre minimum de tours requis pour ouvrir le cadenas, ou -1 s'il est impossible.

## 🔍 Exemples

**Exemple 1 :**
```
Input: deadends = ["0201","0101","0102","1212","2002"], target = "0202"
Output: 6
Explication:
Une séquence de mouvements valides serait "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202".
Notez que une séquence comme "0000" -> "0001" -> "0002" -> "0102" -> ... ne serait pas valide,
car le code du cadenas sera bloqué après avoir affiché "0102".
```

**Exemple 2 :**
```
Input: deadends = ["8888"], target = "0009"
Output: 1
Explication: On peut tourner la dernière molette dans le sens inverse : "0000" -> "0009".
```

**Exemple 3 :**
```
Input: deadends = ["8887","8889","8878","8898","8788","8988","7888","9888"], target = "8888"
Output: -1
Explication: On ne peut pas atteindre la cible sans passer par un deadend.
```

## 🎯 Contraintes

- `1 <= deadends.length <= 500`
- `deadends[i].length == 4`
- `target.length == 4`
- `target` ne sera pas dans la liste des `deadends`.
- `target` et `deadends[i]` consistent uniquement en chiffres.

## 💡 Indices

<details>
<summary>Indice 1</summary>

Traitez ceci comme un problème de graphe où chaque état du cadenas est un nœud, et les arêtes connectent les états adjacents.

</details>

<details>
<summary>Indice 2</summary>

Utilisez BFS pour trouver le chemin le plus court de "0000" au target, en évitant les deadends.

</details>

<details>
<summary>Indice 3</summary>

Pour chaque état, vous pouvez générer 8 états suivants (tourner chaque molette dans les deux sens).

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func openLock(deadends []string, target string) int {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        deadends []string
        target   string
        expected int
    }{
        {
            []string{"0201", "0101", "0102", "1212", "2002"},
            "0202",
            6,
        },
        {
            []string{"8888"},
            "0009",
            1,
        },
        {
            []string{"8887", "8889", "8878", "8898", "8788", "8988", "7888", "9888"},
            "8888",
            -1,
        },
        {
            []string{"0000"},
            "8888",
            -1,
        },
        {
            []string{},
            "0000",
            0,
        },
        {
            []string{},
            "0001",
            1,
        },
        {
            []string{"0001", "0010", "0100", "1000"},
            "0002",
            6,
        },
    }

    fmt.Println("=== Tests Open the Lock ===")
    for i, test := range tests {
        result := openLock(test.deadends, test.target)
        status := "✅"
        if result != test.expected {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Deadends: %v\n", test.deadends)
        fmt.Printf("  Target: %s\n", test.target)
        fmt.Printf("  Expected: %d, Got: %d\n\n", test.expected, result)
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func openLock(deadends []string, target string) int {
    // Convertir deadends en set pour recherche O(1)
    deadSet := make(map[string]bool)
    for _, dead := range deadends {
        deadSet[dead] = true
    }

    // Si le point de départ est un deadend
    if deadSet["0000"] {
        return -1
    }

    // Si on est déjà à la cible
    if target == "0000" {
        return 0
    }

    // BFS
    queue := []string{"0000"}
    visited := make(map[string]bool)
    visited["0000"] = true
    steps := 0

    for len(queue) > 0 {
        size := len(queue)
        steps++

        // Traiter tous les nœuds du niveau actuel
        for i := 0; i < size; i++ {
            current := queue[0]
            queue = queue[1:]

            // Générer tous les états suivants possibles
            neighbors := getNeighbors(current)
            for _, neighbor := range neighbors {
                if neighbor == target {
                    return steps
                }

                if !visited[neighbor] && !deadSet[neighbor] {
                    visited[neighbor] = true
                    queue = append(queue, neighbor)
                }
            }
        }
    }

    return -1
}

func getNeighbors(state string) []string {
    neighbors := []string{}
    bytes := []byte(state)

    for i := 0; i < 4; i++ {
        originalChar := bytes[i]

        // Tourner vers le haut (0->1, 9->0)
        if bytes[i] == '9' {
            bytes[i] = '0'
        } else {
            bytes[i]++
        }
        neighbors = append(neighbors, string(bytes))

        // Restaurer et tourner vers le bas (0->9, 1->0)
        bytes[i] = originalChar
        if bytes[i] == '0' {
            bytes[i] = '9'
        } else {
            bytes[i]--
        }
        neighbors = append(neighbors, string(bytes))

        // Restaurer pour la prochaine molette
        bytes[i] = originalChar
    }

    return neighbors
}
```

**Explication :**
1. **Modélisation en graphe** : Chaque état du cadenas est un nœud
2. **États adjacents** : 8 voisins pour chaque état (4 molettes × 2 directions)
3. **BFS niveau par niveau** : Garantit le nombre minimum de tours
4. **Évitement des deadends** : Vérifier avant d'ajouter à la queue

**Complexité :**
- Temps : O(10^4) = O(1) - au maximum 10000 états possibles
- Espace : O(10^4) = O(1) - pour visited et queue

</details>

## 🚀 Exercices similaires

- [Word Ladder](word-ladder.md)
- [Rotting Oranges](rotting-oranges.md)
- [01 Matrix](01-matrix.md)