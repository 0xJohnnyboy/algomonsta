# Cours 8 - Dynamic Programming Pattern

## 🎯 Quand utiliser ce pattern ?

La **Dynamic Programming (DP)** est essentielle pour :
- **Optimisation** avec sous-problèmes qui se chevauchent
- **Problèmes de comptage** (nombre de façons de faire quelque chose)
- **Problèmes de décision** (min/max, possible/impossible)
- **Séquences** et **sous-séquences** optimales
- **Transformation** d'une structure en une autre

## 🔍 Comment identifier ce pattern

**Mots-clés dans l'énoncé :**
- "optimal", "minimum", "maximum"
- "number of ways", "how many ways"
- "longest", "shortest"
- "subsequence", "substring"
- "fibonacci", "climbing stairs"
- "coin change", "knapsack"

**Signaux :**
- Problème d'optimisation avec choix à chaque étape
- Structure récursive avec sous-problèmes répétés
- "Brute force serait trop lent" (factoriel, exponentiel)

## 🧠 Concepts fondamentaux

### Conditions pour la DP
1. **Optimal Substructure** : La solution optimale contient des solutions optimales aux sous-problèmes
2. **Overlapping Subproblems** : Les mêmes sous-problèmes sont résolus plusieurs fois

### Approches
1. **Top-Down (Memoization)** : Récursion + cache
2. **Bottom-Up (Tabulation)** : Itératif, construire la table DP

## 📋 Templates de base

### Template 1 - Memoization (Top-Down)

```go
func dpTopDown(input InputType) ReturnType {
    memo := make(map[StateType]ReturnType)
    
    var dp func(StateType) ReturnType
    dp = func(state StateType) ReturnType {
        // Cas de base
        if isBaseCase(state) {
            return baseValue
        }
        
        // Vérifier le cache
        if result, exists := memo[state]; exists {
            return result
        }
        
        // Calculer le résultat
        result := computeResult(state, dp)
        
        // Mettre en cache
        memo[state] = result
        return result
    }
    
    return dp(initialState)
}
```

### Template 2 - Tabulation (Bottom-Up)

```go
func dpBottomUp(input InputType) ReturnType {
    // Créer la table DP
    dp := make([]ReturnType, size)
    
    // Initialiser les cas de base
    dp[0] = baseValue
    
    // Remplir la table
    for i := 1; i < size; i++ {
        dp[i] = computeFromPrevious(dp, i)
    }
    
    return dp[size-1]
}
```

### Template 3 - DP 2D

```go
func dp2D(input1, input2 InputType) ReturnType {
    m, n := len(input1), len(input2)
    dp := make([][]ReturnType, m+1)
    for i := range dp {
        dp[i] = make([]ReturnType, n+1)
    }
    
    // Initialiser les cas de base
    for i := 0; i <= m; i++ {
        dp[i][0] = baseValue
    }
    for j := 0; j <= n; j++ {
        dp[0][j] = baseValue
    }
    
    // Remplir la table
    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            dp[i][j] = computeFromAdjacent(dp, i, j, input1, input2)
        }
    }
    
    return dp[m][n]
}
```

## 🚀 Exemple 1 - Fibonacci (Introduction)

**Problème :** Calculer le n-ième nombre de Fibonacci.

**Version naïve (exponentielle) :**
```go
func fibNaive(n int) int {
    if n <= 1 {
        return n
    }
    return fibNaive(n-1) + fibNaive(n-2)
}
```

**Version Memoization :**
```go
func fibMemo(n int) int {
    memo := make(map[int]int)
    
    var fib func(int) int
    fib = func(n int) int {
        if n <= 1 {
            return n
        }
        
        if result, exists := memo[n]; exists {
            return result
        }
        
        result := fib(n-1) + fib(n-2)
        memo[n] = result
        return result
    }
    
    return fib(n)
}
```

**Version Tabulation :**
```go
func fibTab(n int) int {
    if n <= 1 {
        return n
    }
    
    dp := make([]int, n+1)
    dp[0], dp[1] = 0, 1
    
    for i := 2; i <= n; i++ {
        dp[i] = dp[i-1] + dp[i-2]
    }
    
    return dp[n]
}
```

**Version optimisée en espace :**
```go
func fibOptimal(n int) int {
    if n <= 1 {
        return n
    }
    
    prev2, prev1 := 0, 1
    
    for i := 2; i <= n; i++ {
        current := prev1 + prev2
        prev2, prev1 = prev1, current
    }
    
    return prev1
}
```

## 🚀 Exemple 2 - Climbing Stairs

**Problème :** Combien de façons de monter n marches (1 ou 2 marches à la fois) ?

```go
func climbStairs(n int) int {
    if n <= 2 {
        return n
    }
    
    dp := make([]int, n+1)
    dp[1], dp[2] = 1, 2
    
    for i := 3; i <= n; i++ {
        dp[i] = dp[i-1] + dp[i-2]
    }
    
    return dp[n]
}

// Version optimisée
func climbStairsOptimal(n int) int {
    if n <= 2 {
        return n
    }
    
    first, second := 1, 2
    
    for i := 3; i <= n; i++ {
        third := first + second
        first, second = second, third
    }
    
    return second
}
```

## 🚀 Exemple 3 - Coin Change

**Problème :** Nombre minimum de pièces pour faire un montant donné.

```go
func coinChange(coins []int, amount int) int {
    dp := make([]int, amount+1)
    
    // Initialiser avec une valeur impossible
    for i := 1; i <= amount; i++ {
        dp[i] = amount + 1
    }
    
    dp[0] = 0 // Base case: 0 pièce pour montant 0
    
    for i := 1; i <= amount; i++ {
        for _, coin := range coins {
            if coin <= i {
                dp[i] = min(dp[i], dp[i-coin]+1)
            }
        }
    }
    
    if dp[amount] > amount {
        return -1 // Impossible
    }
    
    return dp[amount]
}

func min(a, b int) int {
    if a < b { return a }
    return b
}
```

## 🚀 Exemple 4 - Longest Common Subsequence (LCS)

**Problème :** Longueur de la plus longue sous-séquence commune.

```go
func longestCommonSubsequence(text1 string, text2 string) int {
    m, n := len(text1), len(text2)
    dp := make([][]int, m+1)
    for i := range dp {
        dp[i] = make([]int, n+1)
    }
    
    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if text1[i-1] == text2[j-1] {
                dp[i][j] = dp[i-1][j-1] + 1
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
            }
        }
    }
    
    return dp[m][n]
}

func max(a, b int) int {
    if a > b { return a }
    return b
}
```

## 🚀 Exemple 5 - House Robber

**Problème :** Maximum d'argent volable sans voler deux maisons adjacentes.

```go
func rob(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    if len(nums) == 1 {
        return nums[0]
    }
    
    prev2, prev1 := 0, nums[0]
    
    for i := 1; i < len(nums); i++ {
        current := max(prev1, prev2+nums[i])
        prev2, prev1 = prev1, current
    }
    
    return prev1
}
```

## 📊 Complexité

### Analyse générale
- **Memoization** : O(nombre d'états uniques × temps par état)
- **Tabulation** : O(taille de la table × temps par cellule)
- **Espace** : O(taille de la table) ou optimisable selon les dépendances

### Exemples spécifiques
- **Fibonacci** : O(n) temps, O(1) espace (optimisé)
- **LCS** : O(m×n) temps, O(m×n) espace
- **Coin Change** : O(amount × coins) temps, O(amount) espace
- **Knapsack** : O(n × capacity) temps, O(capacity) espace (optimisé)

## ⚠️ Pièges courants

1. **État mal défini** - Identifier correctement ce qui caractérise un sous-problème
2. **Cas de base oubliés** - Bien initialiser la table DP
3. **Ordre de remplissage** - S'assurer que les dépendances sont calculées avant
4. **Index off-by-one** - Attention aux indices dans les tableaux 2D
5. **Optimisation d'espace prématurée** - Commencer par la version simple

## 🔄 Patterns courants

### 1. Linear DP (1D)
- **Caractéristique** : `dp[i]` dépend de `dp[i-1]`, `dp[i-2]`, etc.
- **Exemples** : Fibonacci, Climbing Stairs, House Robber

### 2. Grid DP (2D)
- **Caractéristique** : `dp[i][j]` dépend de ses voisins
- **Exemples** : Unique Paths, Minimum Path Sum

### 3. Interval DP
- **Caractéristique** : Problèmes sur des intervalles `[i, j]`
- **Exemples** : Matrix Chain Multiplication, Palindrome Partitioning

### 4. Tree DP
- **Caractéristique** : DP sur des arbres
- **Exemples** : House Robber III, Binary Tree Cameras

### 5. State Machine DP
- **Caractéristique** : États avec transitions
- **Exemples** : Best Time to Buy/Sell Stock with Cooldown

## 🏃‍♂️ Exercices pratiques

<!-- ### Easy -->
<!-- - [Climbing Stairs](../exercises/dynamic-programming/easy/climbing-stairs.md) -->
<!-- - [House Robber](../exercises/dynamic-programming/easy/house-robber.md) -->
<!-- - [Maximum Subarray](../exercises/dynamic-programming/easy/max-subarray.md) -->

### Medium
- [Coin Change](../exercises/dynamic-programming/medium/coin-change.md)
<!-- - [Longest Common Subsequence](../exercises/dynamic-programming/medium/lcs.md) -->
<!-- - [Unique Paths](../exercises/dynamic-programming/medium/unique-paths.md) -->
<!-- - [Word Break](../exercises/dynamic-programming/medium/word-break.md) -->
<!-- - [0/1 Knapsack](../exercises/dynamic-programming/medium/knapsack.md) -->
<!---->
<!-- ### Hard -->
<!-- - [Edit Distance](../exercises/dynamic-programming/hard/edit-distance.md) -->
<!-- - [Regular Expression Matching](../exercises/dynamic-programming/hard/regex-matching.md) -->
<!-- - [Burst Balloons](../exercises/dynamic-programming/hard/burst-balloons.md) -->

## 🧠 Stratégie de résolution

1. **Identifier la structure récursive** :
   - Quels sont les sous-problèmes ?
   - Comment une solution se décompose-t-elle ?

2. **Définir l'état DP** :
   - Quelles variables définissent un sous-problème ?
   - 1D, 2D, ou plus de dimensions ?

3. **Établir la relation de récurrence** :
   - Comment `dp[i]` se calcule-t-il à partir des états précédents ?

4. **Déterminer les cas de base** :
   - Quels sont les plus petits sous-problèmes ?

5. **Choisir l'implémentation** :
   - Top-down (récursion + memo) ou bottom-up (itératif) ?

6. **Optimiser si nécessaire** :
   - Peut-on réduire l'espace utilisé ?
   - Y a-t-il des calculs redondants ?

## 💡 Conseils pour maîtriser la DP

### ✅ Bonnes pratiques
- **Dessinez** des exemples pour comprendre la récurrence
- **Commencez** par la version récursive naïve
- **Ajoutez** la mémorisation progressivement
- **Optimisez** l'espace en dernier
- **Testez** avec des cas simples d'abord

### ❌ Erreurs à éviter
- Foncer dans l'implémentation sans comprendre la récurrence
- Optimiser prématurément (espace avant correction)
- Ignorer les cas de base ou les initialiser incorrectement
- Ne pas vérifier la logique sur des exemples simples
- Confondre les indices (off-by-one errors)

## ➡️ Cours suivant

[Cours 9 - Advanced Patterns](09-advanced-patterns.md) - Techniques spécialisées et patterns avancés pour les problèmes complexes.
