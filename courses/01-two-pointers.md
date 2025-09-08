# Cours 1 - Two Pointers Pattern

## 🎯 Quand utiliser ce pattern ?

Le pattern **Two Pointers** est utile quand :
- Vous travaillez avec des **tableaux ou chaînes triés**
- Vous cherchez une **paire d'éléments** qui satisfait une condition
- Vous voulez **optimiser un algorithme O(n²)** vers O(n)
- Vous devez **comparer des éléments** depuis les extrémités

## 🔍 Comment identifier ce pattern

**Mots-clés dans l'énoncé :**
- "paire de nombres", "deux éléments"
- "tableau trié", "chaîne triée"
- "somme égale à target"
- "palindrome"
- "reverse"

## 📋 Template de base

```go
func twoPointers(arr []int) bool {
    left, right := 0, len(arr)-1
    
    for left < right {
        // Logique selon le problème
        if condition {
            // Traiter la solution
            left++
            right--
        } else if needMoveLeft {
            left++
        } else {
            right--
        }
    }
    
    return false
}
```

## 🚀 Exemple 1 - Two Sum (Tableau trié)

**Problème :** Trouver deux nombres dans un tableau trié dont la somme égale target.

```go
func twoSum(numbers []int, target int) []int {
    left, right := 0, len(numbers)-1
    
    for left < right {
        sum := numbers[left] + numbers[right]
        
        if sum == target {
            return []int{left + 1, right + 1} // indices 1-based
        } else if sum < target {
            left++ // On a besoin d'une somme plus grande
        } else {
            right-- // On a besoin d'une somme plus petite
        }
    }
    
    return []int{} // Pas de solution
}
```

**Pourquoi ça marche :** 
- Tableau trié → si somme trop petite, augmenter left
- Si somme trop grande, diminuer right
- Complexité : O(n) vs O(n²) naïf

## 🚀 Exemple 2 - Valid Palindrome

**Problème :** Vérifier si une chaîne est un palindrome (ignorer espaces/ponctuation).

```go
import (
    "strings"
    "unicode"
)

func isPalindrome(s string) bool {
    // Nettoyer la chaîne
    clean := ""
    for _, char := range strings.ToLower(s) {
        if unicode.IsLetter(char) || unicode.IsDigit(char) {
            clean += string(char)
        }
    }
    
    left, right := 0, len(clean)-1
    
    for left < right {
        if clean[left] != clean[right] {
            return false
        }
        left++
        right--
    }
    
    return true
}
```

## 🚀 Exemple 3 - Remove Duplicates

**Problème :** Supprimer les doublons d'un tableau trié en place.

```go
func removeDuplicates(nums []int) int {
    if len(nums) <= 1 {
        return len(nums)
    }
    
    slow := 0 // Pointeur pour les éléments uniques
    
    for fast := 1; fast < len(nums); fast++ {
        if nums[fast] != nums[slow] {
            slow++
            nums[slow] = nums[fast]
        }
    }
    
    return slow + 1 // Nouvelle longueur
}
```

## 📊 Complexité

- **Temporelle :** O(n) - un seul passage
- **Spatiale :** O(1) - pas d'espace supplémentaire

## ⚠️ Pièges courants

1. **Indices hors limites** - Toujours vérifier `left < right`
2. **Boucle infinie** - S'assurer que les pointeurs bougent
3. **Tableau non trié** - Le pattern ne fonctionne que sur données triées
4. **Indices 0-based vs 1-based** - Bien lire l'énoncé

## 🏃‍♂️ Exercices pratiques

### Easy
- [Two Sum II (Sorted Array)](../exercises/two-pointers/easy/two-sum-ii.md)
<!-- - [Valid Palindrome](../exercises/two-pointers/easy/valid-palindrome.md) -->
<!-- - [Remove Duplicates](../exercises/two-pointers/easy/remove-duplicates.md) -->
<!---->
<!-- ### Medium   -->
<!-- - [3Sum](../exercises/two-pointers/medium/three-sum.md) -->
<!-- - [Container With Most Water](../exercises/two-pointers/medium/container-water.md) -->
<!-- - [Sort Colors](../exercises/two-pointers/medium/sort-colors.md) -->
<!---->
<!-- ### Hard -->
<!-- - [Trapping Rain Water](../exercises/two-pointers/hard/trapping-rain-water.md) -->

## ➡️ Cours suivant

[Cours 2 - Sliding Window Pattern](02-sliding-window.md) - Pour optimiser les problèmes de sous-tableaux.
