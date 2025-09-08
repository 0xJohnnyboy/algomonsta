# Exercice - Generate Parentheses

**Niveau :** Medium  
**Pattern :** Backtracking  
**Retour au cours :** [Backtracking Pattern](../../courses/07-backtracking.md)

## 📝 Énoncé

Étant donné `n` paires de parenthèses, écrivez une fonction pour **générer toutes les combinaisons** de parenthèses bien formées.

## 🔍 Exemples

**Exemple 1 :**
```
Input: n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]
```

**Exemple 2 :**
```
Input: n = 1  
Output: ["()"]
```

## 🎯 Contraintes

- `1 <= n <= 8`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez le backtracking pour construire les chaînes caractère par caractère. À chaque étape, vous pouvez ajouter soit '(' soit ')'.

</details>

<details>
<summary>Indice 2</summary>

Maintenez le compte des parenthèses ouvrantes et fermantes utilisées. Vous ne pouvez pas avoir plus de n parenthèses de chaque type.

</details>

<details>
<summary>Indice 3</summary>

**Contrainte importante** : Vous ne pouvez ajouter ')' que si vous avez plus de '(' que de ')' dans la chaîne actuelle.

</details>

<details>
<summary>Indice 4</summary>

Conditions pour ajouter des parenthèses :
- Ajouter '(' : si `openCount < n`
- Ajouter ')' : si `closeCount < openCount`

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func generateParenthesis(n int) []string {
    // Votre code ici
    
}

func main() {
    // Test cases
    fmt.Println("n=1:", generateParenthesis(1))
    // Expected: ["()"]
    
    fmt.Println("n=2:", generateParenthesis(2))  
    // Expected: ["(())","()()"]
    
    fmt.Println("n=3:", generateParenthesis(3))
    // Expected: ["((()))","(()())","(())()","()(())","()()()"]
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func generateParenthesis(n int) []string {
    var result []string
    
    var backtrack func(string, int, int)
    backtrack = func(current string, openCount, closeCount int) {
        // Cas de base - chaîne complète
        if len(current) == 2*n {
            result = append(result, current)
            return
        }
        
        // Ajouter '(' si possible
        if openCount < n {
            backtrack(current+"(", openCount+1, closeCount)
        }
        
        // Ajouter ')' si possible et valide
        if closeCount < openCount {
            backtrack(current+")", openCount, closeCount+1)
        }
    }
    
    backtrack("", 0, 0)
    return result
}
```

**Version alternative avec slice de bytes (plus efficace) :**

```go
func generateParenthesis(n int) []string {
    var result []string
    current := make([]byte, 0, 2*n)
    
    var backtrack func(int, int)
    backtrack = func(openCount, closeCount int) {
        // Cas de base
        if len(current) == 2*n {
            result = append(result, string(current))
            return
        }
        
        // Ajouter '('
        if openCount < n {
            current = append(current, '(')
            backtrack(openCount+1, closeCount)
            current = current[:len(current)-1] // Backtrack
        }
        
        // Ajouter ')'
        if closeCount < openCount {
            current = append(current, ')')
            backtrack(openCount, closeCount+1)
            current = current[:len(current)-1] // Backtrack
        }
    }
    
    backtrack(0, 0)
    return result
}
```

**Simulation pour n=2 :**

```
backtrack("", 0, 0):
  Peut ajouter '(' (0 < 2) → backtrack("(", 1, 0)
    Peut ajouter '(' (1 < 2) → backtrack("((", 2, 0)
      Ne peut pas ajouter '(' (2 = 2)
      Peut ajouter ')' (0 < 2) → backtrack("(()", 2, 1)
        Ne peut pas ajouter '(' (2 = 2)
        Peut ajouter ')' (1 < 2) → backtrack("(())", 2, 2)
          Longueur = 4 = 2*2 → SOLUTION: "(())"
    Peut ajouter ')' (0 < 1) → backtrack("()", 1, 1)
      Peut ajouter '(' (1 < 2) → backtrack("()(", 2, 1)
        Ne peut pas ajouter '(' (2 = 2)  
        Peut ajouter ')' (1 < 2) → backtrack("()()", 2, 2)
          Longueur = 4 = 2*2 → SOLUTION: "()()"

Résultat: ["(())", "()()"]
```

**Explication de l'algorithme :**

1. **État** : chaîne actuelle + compteurs d'ouvertures et fermetures
2. **Choix** : ajouter '(' ou ')' selon les contraintes
3. **Contraintes** :
   - `openCount < n` : peut ajouter '('
   - `closeCount < openCount` : peut ajouter ')' (pour garder la validité)
4. **Cas de base** : quand la longueur atteint 2n

**Pourquoi ces contraintes garantissent des parenthèses valides ?**
- On ne peut jamais avoir plus de ')' que de '(' à un moment donné
- On utilise exactement n parenthèses de chaque type
- Donc chaque ')' a sa '(' correspondante à sa gauche

**Complexité :**
- Temps : O(4^n / √n) - nombre de séquences valides (nombre de Catalan)
- Espace : O(4^n / √n) pour stocker les résultats + O(n) pour la pile de récursion

**Optimisations possibles :**
1. **Mémorisation** : Pas utile ici car pas de sous-problèmes répétés
2. **Génération directe** : Utiliser les nombres de Catalan
3. **Itératif** : Transformer en version itérative avec une pile

</details>

## 🎯 Points clés à retenir

1. **Contraintes de validité** : Ne jamais avoir plus de ')' que de '(' en cours
2. **Compteurs** : Suivre le nombre d'ouvertures et fermetures utilisées
3. **Cas de base** : Longueur totale = 2n
4. **Backtracking implicite** : Les paramètres de fonction créent l'effet de backtrack

## 🚀 Exercices similaires

- [Valid Parentheses](../easy/valid-parentheses.md) - Vérifier la validité (plus simple)
- [Remove Invalid Parentheses](../hard/remove-invalid.md) - Retirer pour rendre valide
- [Different Ways to Add Parentheses](../medium/different-ways.md) - Parenthèses dans expressions

## 🔍 Variations

**Avec différents types de parenthèses :**
```go
// Générer avec (), [], {}
func generateMixedParentheses(n int) []string {
    // Logique similaire mais avec 3 types
}
```

**Avec contraintes supplémentaires :**
```go
// Maximum de parenthèses consécutives
func generateWithMaxConsecutive(n, maxConsec int) []string {
    // Ajouter une contrainte sur les séquences consécutives
}
```

## 🎓 Concept mathématique

Ce problème génère les **nombres de Catalan** :
- C₀ = 1
- C₁ = 1  
- C₂ = 2
- C₃ = 5
- Cₙ = C₀Cₙ₋₁ + C₁Cₙ₋₂ + ... + Cₙ₋₁C₀

Les parenthèses valides avec n paires correspondent exactement au n-ième nombre de Catalan.
