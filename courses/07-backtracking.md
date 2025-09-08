# Cours 7 - Backtracking Pattern

## 🎯 Quand utiliser ce pattern ?

Le **Backtracking** est parfait pour :
- **Énumération exhaustive** de toutes les solutions possibles
- **Problèmes combinatoires** (permutations, combinaisons, sous-ensembles)
- **Placement avec contraintes** (N-Queens, Sudoku)
- **Exploration de chemins** avec retour en arrière
- **Optimisation** quand on doit explorer toutes les possibilités

## 🔍 Comment identifier ce pattern

**Mots-clés dans l'énoncé :**
- "all possible", "find all", "generate all"
- "permutations", "combinations", "subsets"
- "valid arrangements", "satisfy constraints"
- "place n queens", "solve sudoku"
- "word search", "path exists"

**Signal principal :** Besoin d'explorer toutes les possibilités avec possibilité de retour en arrière.

## 🧠 Concept fondamental

Le backtracking suit ce schéma :
1. **Choisir** - Faire un choix
2. **Explorer** - Continuer récursivement avec ce choix
3. **Revenir en arrière** - Annuler le choix si ça ne mène pas à une solution

## 📋 Template de base

### Template 1 - Backtracking Standard

```go
func backtrack(result *[]Solution, current Solution, choices []Choice) {
    // Cas de base - solution complète
    if isComplete(current) {
        // Ajouter une copie de la solution
        solution := make(Solution, len(current))
        copy(solution, current)
        *result = append(*result, solution)
        return
    }
    
    // Explorer chaque choix possible
    for _, choice := range choices {
        if isValid(current, choice) {
            // Faire le choix
            current = append(current, choice)
            
            // Explorer récursivement
            backtrack(result, current, getNextChoices(choice))
            
            // Revenir en arrière (backtrack)
            current = current[:len(current)-1]
        }
    }
}
```

### Template 2 - Backtracking avec état modifiable

```go
func backtrackWithState(result *[]Solution, state *State) {
    if isComplete(state) {
        *result = append(*result, cloneState(state))
        return
    }
    
    for _, choice := range getPossibleChoices(state) {
        if isValid(state, choice) {
            // Faire le choix
            applyChoice(state, choice)
            
            // Explorer
            backtrackWithState(result, state)
            
            // Revenir en arrière
            undoChoice(state, choice)
        }
    }
}
```

## 🚀 Exemple 1 - Generate Subsets

**Problème :** Générer tous les sous-ensembles d'un tableau.

```go
func subsets(nums []int) [][]int {
    var result [][]int
    var current []int
    
    var backtrack func(int)
    backtrack = func(start int) {
        // Ajouter le sous-ensemble actuel
        subset := make([]int, len(current))
        copy(subset, current)
        result = append(result, subset)
        
        // Explorer en ajoutant chaque élément suivant
        for i := start; i < len(nums); i++ {
            current = append(current, nums[i])    // Choisir
            backtrack(i + 1)                      // Explorer
            current = current[:len(current)-1]    // Backtrack
        }
    }
    
    backtrack(0)
    return result
}
```

## 🚀 Exemple 2 - Permutations

**Problème :** Générer toutes les permutations d'un tableau.

```go
func permute(nums []int) [][]int {
    var result [][]int
    var current []int
    used := make([]bool, len(nums))
    
    var backtrack func()
    backtrack = func() {
        // Cas de base - permutation complète
        if len(current) == len(nums) {
            perm := make([]int, len(current))
            copy(perm, current)
            result = append(result, perm)
            return
        }
        
        // Essayer chaque nombre non utilisé
        for i := 0; i < len(nums); i++ {
            if used[i] {
                continue
            }
            
            // Choisir
            current = append(current, nums[i])
            used[i] = true
            
            // Explorer
            backtrack()
            
            // Backtrack
            current = current[:len(current)-1]
            used[i] = false
        }
    }
    
    backtrack()
    return result
}
```

## 🚀 Exemple 3 - N-Queens

**Problème :** Placer N reines sur un échiquier N×N sans qu'elles s'attaquent.

```go
func solveNQueens(n int) [][]string {
    var result [][]string
    board := make([][]byte, n)
    for i := range board {
        board[i] = make([]byte, n)
        for j := range board[i] {
            board[i][j] = '.'
        }
    }
    
    var backtrack func(int)
    backtrack = func(row int) {
        // Cas de base - toutes les reines placées
        if row == n {
            solution := make([]string, n)
            for i := 0; i < n; i++ {
                solution[i] = string(board[i])
            }
            result = append(result, solution)
            return
        }
        
        // Essayer de placer une reine dans chaque colonne
        for col := 0; col < n; col++ {
            if isValidPosition(board, row, col) {
                // Placer la reine
                board[row][col] = 'Q'
                
                // Explorer la ligne suivante
                backtrack(row + 1)
                
                // Retirer la reine (backtrack)
                board[row][col] = '.'
            }
        }
    }
    
    backtrack(0)
    return result
}

func isValidPosition(board [][]byte, row, col int) bool {
    n := len(board)
    
    // Vérifier la colonne
    for i := 0; i < row; i++ {
        if board[i][col] == 'Q' {
            return false
        }
    }
    
    // Vérifier la diagonale principale
    for i, j := row-1, col-1; i >= 0 && j >= 0; i, j = i-1, j-1 {
        if board[i][j] == 'Q' {
            return false
        }
    }
    
    // Vérifier la diagonale secondaire
    for i, j := row-1, col+1; i >= 0 && j < n; i, j = i-1, j+1 {
        if board[i][j] == 'Q' {
            return false
        }
    }
    
    return true
}
```

## 🚀 Exemple 4 - Word Search

**Problème :** Chercher si un mot existe dans une grille de lettres.

```go
func exist(board [][]byte, word string) bool {
    rows, cols := len(board), len(board[0])
    
    var backtrack func(int, int, int) bool
    backtrack = func(row, col, index int) bool {
        // Cas de base - mot trouvé
        if index == len(word) {
            return true
        }
        
        // Vérifier les limites et la lettre
        if row < 0 || row >= rows || col < 0 || col >= cols || 
           board[row][col] != word[index] {
            return false
        }
        
        // Marquer comme visité
        temp := board[row][col]
        board[row][col] = '#'
        
        // Explorer les 4 directions
        found := backtrack(row+1, col, index+1) ||
                backtrack(row-1, col, index+1) ||
                backtrack(row, col+1, index+1) ||
                backtrack(row, col-1, index+1)
        
        // Restaurer la cellule (backtrack)
        board[row][col] = temp
        
        return found
    }
    
    // Essayer depuis chaque position
    for i := 0; i < rows; i++ {
        for j := 0; j < cols; j++ {
            if backtrack(i, j, 0) {
                return true
            }
        }
    }
    
    return false
}
```

## 📊 Complexité

- **Temporelle :** Généralement exponentielle O(b^d) où :
  - b = facteur de branchement (nombre de choix à chaque étape)
  - d = profondeur de l'arbre de recherche
- **Spatiale :** O(d) pour la pile de récursion

**Exemples spécifiques :**
- Subsets: O(2^n) - chaque élément peut être inclus ou non
- Permutations: O(n!) - n choix, puis n-1, etc.
- N-Queens: O(n!) dans le pire cas

## ⚠️ Pièges courants

1. **Oublier le backtrack** - Ne pas restaurer l'état après exploration
2. **Copie de références** - Ajouter une référence au lieu d'une copie
3. **Modification d'état global** - Attention aux effets de bord
4. **Conditions d'arrêt** - Mal définir quand une solution est complète
5. **Optimisations manquées** - Ne pas utiliser de pruning quand possible

## 🔄 Optimisations courantes

### 1. Pruning (Élagage)
```go
func backtrackWithPruning(current Solution, remaining Choices) {
    if !canLeadToSolution(current, remaining) {
        return // Élaguer cette branche
    }
    
    // Continue normal backtracking...
}
```

### 2. Mémorisation
```go
func backtrackWithMemo(state State, memo map[State]bool) bool {
    if result, exists := memo[state]; exists {
        return result
    }
    
    // Calculer le résultat
    result := normalBacktrack(state)
    memo[state] = result
    return result
}
```

### 3. Optimisation des choix
```go
// Trier les choix pour explorer les plus prometteurs en premier
sort.Slice(choices, func(i, j int) bool {
    return heuristic(choices[i]) > heuristic(choices[j])
})
```

## 🎭 Patterns spécialisés

### Combination Sum
```go
func combinationSum(candidates []int, target int) [][]int {
    var result [][]int
    var current []int
    
    var backtrack func(int, int)
    backtrack = func(start, remaining int) {
        if remaining == 0 {
            combination := make([]int, len(current))
            copy(combination, current)
            result = append(result, combination)
            return
        }
        
        for i := start; i < len(candidates); i++ {
            if candidates[i] <= remaining {
                current = append(current, candidates[i])
                backtrack(i, remaining-candidates[i]) // i, pas i+1 (réutilisable)
                current = current[:len(current)-1]
            }
        }
    }
    
    backtrack(0, target)
    return result
}
```

### Palindrome Partitioning
```go
func partition(s string) [][]string {
    var result [][]string
    var current []string
    
    var backtrack func(int)
    backtrack = func(start int) {
        if start == len(s) {
            partition := make([]string, len(current))
            copy(partition, current)
            result = append(result, partition)
            return
        }
        
        for end := start; end < len(s); end++ {
            if isPalindrome(s, start, end) {
                current = append(current, s[start:end+1])
                backtrack(end + 1)
                current = current[:len(current)-1]
            }
        }
    }
    
    backtrack(0)
    return result
}

func isPalindrome(s string, left, right int) bool {
    for left < right {
        if s[left] != s[right] {
            return false
        }
        left++
        right--
    }
    return true
}
```

## 🏃‍♂️ Exercices pratiques

### Easy
<!-- - [Subsets](../exercises/backtracking/easy/subsets.md) -->
- [Generate Parentheses](../exercises/backtracking/easy/generate-parentheses.md)

<!-- ### Medium -->
<!-- - [Permutations](../exercises/backtracking/medium/permutations.md) -->
<!-- - [Combination Sum](../exercises/backtracking/medium/combination-sum.md) -->
<!-- - [Word Search](../exercises/backtracking/medium/word-search.md) -->
<!-- - [Palindrome Partitioning](../exercises/backtracking/medium/palindrome-partitioning.md) -->
<!---->
<!-- ### Hard -->
<!-- - [N-Queens](../exercises/backtracking/hard/n-queens.md) -->
<!-- - [Sudoku Solver](../exercises/backtracking/hard/sudoku-solver.md) -->
<!-- - [Word Search II](../exercises/backtracking/hard/word-search-ii.md) -->
<!---->
## 🧠 Stratégie de résolution

1. **Identifier la structure du problème** :
   - Quels sont les choix à chaque étape ?
   - Quand une solution est-elle complète ?
   - Quelles sont les contraintes ?

2. **Définir l'état** :
   - Quelles informations maintenir durant la recherche ?
   - Comment représenter une solution partielle ?

3. **Implémenter les fonctions clés** :
   - `isComplete()` - Solution trouvée ?
   - `isValid()` - Choix légal ?
   - `makeChoice()` et `undoChoice()` - Modifier l'état

4. **Optimiser si nécessaire** :
   - Pruning pour éviter les branches inutiles
   - Trier les choix par priorité
   - Mémorisation si sous-problèmes répétés

5. **Tester avec des cas simples** :
   - Vérifier que le backtracking fonctionne
   - S'assurer que tous les cas sont couverts

## ➡️ Cours suivant

[Cours 8 - Dynamic Programming Pattern](08-dynamic-programming.md) - Pour l'optimisation avec mémorisation.
