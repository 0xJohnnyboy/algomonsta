# Exercice - Coin Change

**Niveau :** Medium  
**Pattern :** Dynamic Programming (Optimisation)  
**Retour au cours :** [Dynamic Programming Pattern](../../courses/08-dynamic-programming.md)

## 📝 Énoncé

Vous disposez de pièces de différentes dénominations et d'un montant total d'argent. Écrivez une fonction pour calculer le **nombre minimum de pièces** nécessaires pour composer ce montant. Si ce montant d'argent ne peut pas être composé par une combinaison des pièces, retournez `-1`.

Vous pouvez considérer que vous avez un **nombre infini** de chaque type de pièce.

## 🔍 Exemples

**Exemple 1 :**
```
Input: coins = [1,3,4], amount = 6
Output: 2
Explication: 6 = 3 + 3
```

**Exemple 2 :**
```
Input: coins = [2], amount = 3
Output: -1
Explication: Le montant 3 ne peut pas être composé avec des pièces de 2.
```

**Exemple 3 :**
```
Input: coins = [1], amount = 0
Output: 0
Explication: Aucune pièce nécessaire pour le montant 0.
```

**Exemple 4 :**
```
Input: coins = [1,2,5], amount = 11
Output: 3
Explication: 11 = 5 + 5 + 1
```

## 🎯 Contraintes

- `1 <= coins.length <= 12`
- `1 <= coins[i] <= 2^31 - 1`
- `0 <= amount <= 10^4`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème d'**optimisation** classique. Pour chaque montant, on veut le minimum de pièces. Pensez à la programmation dynamique.

</details>

<details>
<summary>Indice 2</summary>

Définissez `dp[i]` = nombre minimum de pièces pour faire le montant `i`. Comment calculer `dp[i]` à partir des valeurs précédentes ?

</details>

<details>
<summary>Indice 3</summary>

Pour calculer `dp[i]`, essayez chaque pièce `coin`. Si `coin <= i`, alors `dp[i] = min(dp[i], dp[i-coin] + 1)`.

</details>

<details>
<summary>Indice 4</summary>

**Cas de base :** `dp[0] = 0` (0 pièce pour le montant 0). Initialisez les autres valeurs avec une valeur "impossible" comme `amount + 1`.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func coinChange(coins []int, amount int) int {
    // Votre code ici
    
}

func main() {
    // Test cases
    fmt.Println(coinChange([]int{1, 3, 4}, 6))    // Expected: 2
    fmt.Println(coinChange([]int{2}, 3))          // Expected: -1
    fmt.Println(coinChange([]int{1}, 0))          // Expected: 0
    fmt.Println(coinChange([]int{1, 2, 5}, 11))   // Expected: 3
    
    // Test edge cases
    fmt.Println(coinChange([]int{1}, 1))          // Expected: 1
    fmt.Println(coinChange([]int{1, 3, 4}, 0))    // Expected: 0
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func coinChange(coins []int, amount int) int {
    // dp[i] = nombre minimum de pièces pour le montant i
    dp := make([]int, amount+1)
    
    // Initialiser avec une valeur "impossible"
    for i := 1; i <= amount; i++ {
        dp[i] = amount + 1
    }
    
    // Cas de base
    dp[0] = 0
    
    // Remplir la table DP
    for i := 1; i <= amount; i++ {
        for _, coin := range coins {
            if coin <= i {
                dp[i] = min(dp[i], dp[i-coin]+1)
            }
        }
    }
    
    // Si dp[amount] est toujours "impossible", retourner -1
    if dp[amount] > amount {
        return -1
    }
    
    return dp[amount]
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}
```

**Version avec memoization (Top-Down) :**

```go
func coinChangeMemo(coins []int, amount int) int {
    memo := make(map[int]int)
    
    var dp func(int) int
    dp = func(remaining int) int {
        // Cas de base
        if remaining == 0 {
            return 0
        }
        if remaining < 0 {
            return -1
        }
        
        // Vérifier le cache
        if result, exists := memo[remaining]; exists {
            return result
        }
        
        minCoins := amount + 1
        
        // Essayer chaque pièce
        for _, coin := range coins {
            result := dp(remaining - coin)
            if result != -1 {
                minCoins = min(minCoins, result+1)
            }
        }
        
        // Mettre en cache
        if minCoins == amount+1 {
            memo[remaining] = -1
        } else {
            memo[remaining] = minCoins
        }
        
        return memo[remaining]
    }
    
    return dp(amount)
}
```

**Simulation pour coins = [1,3,4], amount = 6 :**

```
Initialisation:
dp = [0, ∞, ∞, ∞, ∞, ∞, ∞]  (∞ = amount + 1 = 7)

i = 1:
  coin = 1: dp[1] = min(7, dp[0] + 1) = min(7, 1) = 1
  coin = 3: 3 > 1, skip
  coin = 4: 4 > 1, skip
  dp = [0, 1, ∞, ∞, ∞, ∞, ∞]

i = 2:
  coin = 1: dp[2] = min(7, dp[1] + 1) = min(7, 2) = 2
  coin = 3: 3 > 2, skip
  coin = 4: 4 > 2, skip
  dp = [0, 1, 2, ∞, ∞, ∞, ∞]

i = 3:
  coin = 1: dp[3] = min(7, dp[2] + 1) = min(7, 3) = 3
  coin = 3: dp[3] = min(3, dp[0] + 1) = min(3, 1) = 1
  coin = 4: 4 > 3, skip
  dp = [0, 1, 2, 1, ∞, ∞, ∞]

i = 4:
  coin = 1: dp[4] = min(7, dp[3] + 1) = min(7, 2) = 2
  coin = 3: dp[4] = min(2, dp[1] + 1) = min(2, 2) = 2
  coin = 4: dp[4] = min(2, dp[0] + 1) = min(2, 1) = 1
  dp = [0, 1, 2, 1, 1, ∞, ∞]

i = 5:
  coin = 1: dp[5] = min(7, dp[4] + 1) = min(7, 2) = 2
  coin = 3: dp[5] = min(2, dp[2] + 1) = min(2, 3) = 2
  coin = 4: dp[5] = min(2, dp[1] + 1) = min(2, 2) = 2
  dp = [0, 1, 2, 1, 1, 2, ∞]

i = 6:
  coin = 1: dp[6] = min(7, dp[5] + 1) = min(7, 3) = 3
  coin = 3: dp[6] = min(3, dp[3] + 1) = min(3, 2) = 2
  coin = 4: dp[6] = min(2, dp[2] + 1) = min(2, 3) = 2
  dp = [0, 1, 2, 1, 1, 2, 2]

Résultat: dp[6] = 2 (optimal: 3 + 3)
```

**Explication de l'algorithme :**

1. **État DP :** `dp[i]` = nombre minimum de pièces pour faire le montant `i`

2. **Relation de récurrence :** 
   ```
   dp[i] = min(dp[i], dp[i-coin] + 1) pour chaque coin ≤ i
   ```

3. **Cas de base :** `dp[0] = 0` (aucune pièce pour montant 0)

4. **Initialisation :** Valeurs impossibles (`amount + 1`) pour détecter l'impossibilité

5. **Résultat :** `dp[amount]` si possible, sinon `-1`

**Complexité :**
- **Temps :** O(amount × coins.length) - Pour chaque montant, on teste chaque pièce
- **Espace :** O(amount) - Tableau DP de taille amount + 1

**Intuition :** 
Pour faire le montant `i`, on peut prendre n'importe quelle pièce `coin` et résoudre le sous-problème `i - coin`. On prend le minimum sur toutes les pièces possibles.

**Points clés :**
1. **Bottom-up :** Construire la solution des petits montants vers les grands
2. **Optimal substructure :** La solution optimale contient des sous-solutions optimales
3. **Overlapping subproblems :** Les mêmes montants sont calculés plusieurs fois

</details>

## 🎯 Points clés à retenir

1. **Optimisation classique :** Minimiser le nombre de pièces
2. **État DP simple :** Une seule dimension (montant)
3. **Initialisation importante :** Valeurs impossibles pour détecter l'échec
4. **Bottom-up naturel :** Construire depuis montant 0

## 🚀 Exercices similaires

- [Coin Change 2](../medium/coin-change-2.md) - Compter le nombre de façons (au lieu du minimum)
- [Perfect Squares](../medium/perfect-squares.md) - Même pattern avec carrés parfaits
- [Minimum Cost For Tickets](../medium/min-cost-tickets.md) - Optimisation avec contraintes temporelles

## 🔍 Variations

**Coin Change 2 - Nombre de façons :**
```go
func change(amount int, coins []int) int {
    dp := make([]int, amount+1)
    dp[0] = 1 // Une façon de faire 0: ne prendre aucune pièce
    
    for _, coin := range coins {
        for i := coin; i <= amount; i++ {
            dp[i] += dp[i-coin]
        }
    }
    
    return dp[amount]
}
```

**Avec reconstruction du chemin :**
```go
func coinChangeWithPath(coins []int, amount int) (int, []int) {
    dp := make([]int, amount+1)
    parent := make([]int, amount+1)
    
    for i := 1; i <= amount; i++ {
        dp[i] = amount + 1
        parent[i] = -1
    }
    
    for i := 1; i <= amount; i++ {
        for _, coin := range coins {
            if coin <= i && dp[i-coin]+1 < dp[i] {
                dp[i] = dp[i-coin] + 1
                parent[i] = coin
            }
        }
    }
    
    if dp[amount] > amount {
        return -1, nil
    }
    
    // Reconstruire le chemin
    path := []int{}
    curr := amount
    for curr > 0 {
        coin := parent[curr]
        path = append(path, coin)
        curr -= coin
    }
    
    return dp[amount], path
}
```

## 🎓 Concepts DP illustrés

- ✅ **Optimal Substructure** - Solution optimale = sous-solutions optimales
- ✅ **Overlapping Subproblems** - Mêmes montants calculés plusieurs fois  
- ✅ **Bottom-up Construction** - Des petits vers les grands montants
- ✅ **State Space** - Montant comme unique dimension d'état
