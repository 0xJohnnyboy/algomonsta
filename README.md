# 🚀 Algomonsta - algo speed run en Go

Un cours d'algorithmique structuré par patterns, conçu pour la préparation aux entretiens techniques et l'amélioration des compétences en résolution de problèmes.

## 🎯 Objectifs

- **Apprendre par patterns** plutôt que par problèmes isolés
- **Reconnaître rapidement** quel algorithme utiliser
- **Maîtriser Go** pour les entretiens techniques
- **Progression structurée** du débutant à l'expert

## 📚 Structure du cours

### Phase 1 - Fondamentaux 🟢
| Cours | Pattern | Difficulté | Quand l'utiliser |
|-------|---------|------------|------------------|
| [01](courses/01-two-pointers.md) | Two Pointers | ⭐ | Tableaux triés, paires d'éléments |
| [02](courses/02-sliding-window.md) | Sliding Window | ⭐⭐ | Sous-tableaux continus, fenêtres |
| [03](courses/03-binary-search.md) | Binary Search | ⭐⭐ | Recherche dans espaces ordonnés |

### Phase 2 - Structures de données 🟡  
| Cours | Pattern | Difficulté | Quand l'utiliser |
|-------|---------|------------|------------------|
| [04](courses/04-stack-monotonic.md) | Monotonic Stack | ⭐⭐⭐ | Maintenir un ordre spécifique |
| [05](courses/05-dfs-tree.md) | DFS on Trees | ⭐⭐ | Parcours d'arbres en profondeur |
| [06](courses/06-bfs-graph.md) | BFS on Graphs | ⭐⭐⭐ | Plus court chemin, niveau par niveau |

### Phase 3 - Algorithmes avancés 🔴
| Cours | Pattern | Difficulté | Quand l'utiliser |
|-------|---------|------------|------------------|
| [07](courses/07-backtracking.md) | Backtracking | ⭐⭐⭐ | Exploration exhaustive |
| [08](courses/08-dynamic-programming.md) | Dynamic Programming | ⭐⭐⭐⭐ | Sous-problèmes optimaux |
| [09](courses/09-advanced-patterns.md) | Advanced Patterns | ⭐⭐⭐⭐ | Techniques spécialisées |

## 🏁 Guide de démarrage rapide

### 1. Cloner ce repository
```bash
git clone <votre-repo>
cd algo-course
```

### 2. Commencer par le premier cours
```bash
# Lire le cours
cat courses/01-two-pointers.md

# Pratiquer les exercices  
cd exercises/two-pointers/easy
```

### 3. Workflow recommandé
1. **📖 Lire** le cours complet
2. **🧠 Comprendre** les exemples
3. **✍️ Pratiquer** les exercices easy → medium → hard
4. **✅ Implémenter** vos solutions
5. **🔄 Réviser** si nécessaire
6. **➡️ Passer** au cours suivant

## 📁 Organisation des fichiers

```
algo-course/
├── README.md                    # Ce fichier
├── courses/                     # Cours théoriques
│   ├── 01-two-pointers.md
│   ├── 02-sliding-window.md
│   └── ...
└── exercises/                   # Exercices pratiques
    ├── two-pointers/
    │   ├── easy/
    │   │   ├── two-sum-ii.md
    │   │   ├── valid-palindrome.md
    │   │   └── solution.go       # Vos solutions
    │   ├── medium/
    │   └── hard/
    └── ...
```

## 💡 Templates Go utiles

### Structure de base
```go
package main

import (
    "fmt"
    "sort"
)

// Structures de données courantes
type ListNode struct {
    Val  int
    Next *ListNode
}

type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

func main() {
    // Vos tests ici
}
```

### Fonctions utilitaires
```go
func max(a, b int) int {
    if a > b { return a }
    return b
}

func min(a, b int) int {
    if a < b { return a }
    return b
}

func abs(a int) int {
    if a < 0 { return -a }
    return a
}
```

## 📊 Progression suggérée

### Semaine 1-2 : Fondamentaux
- [ ] Two Pointers (3-4 exercices)
- [ ] Sliding Window (4-5 exercices)
- [ ] Binary Search (3-4 exercices)

### Semaine 3-4 : Structures de données
- [ ] Monotonic Stack (2-3 exercices)
- [ ] DFS on Trees (4-5 exercices)  
- [ ] BFS on Graphs (3-4 exercices)

### Semaine 5-6 : Algorithmes avancés
- [ ] Backtracking (3-4 exercices)
- [ ] Dynamic Programming (5-6 exercices)
- [ ] Advanced Patterns (2-3 exercices)

## 🏆 Conseils pour réussir

### ✅ Bonnes pratiques
- **Dessinez** le problème avant de coder
- **Identifiez** le pattern avant d'implémenter
- **Testez** avec plusieurs exemples
- **Analysez** la complexité
- **Refactorisez** votre code

### ❌ Erreurs à éviter
- Ne pas lire l'énoncé complètement
- Coder sans comprendre le pattern
- Ignorer les cas limites
- Ne pas tester le code
- Passer au suivant sans maîtriser

## 🔗 Ressources complémentaires

- [Go Documentation](https://golang.org/doc/)
- [LeetCode](https://leetcode.com/) - Pour plus d'exercices
- [AlgoMonster](https://algo.monster/) - Inspiration originale
- [Big O Cheat Sheet](https://www.bigocheatsheet.com/)

## 📞 Support

Pour toute question ou amélioration :
- Créez une issue sur GitHub
- Ajoutez vos propres exercices
- Partagez vos solutions optimales
