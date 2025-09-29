# Cours 2 - Sliding Window Pattern

## 🎯 Quand utiliser ce pattern ?

Le pattern **Sliding Window** est parfait pour :
- Problèmes sur des **sous-tableaux/sous-chaînes continus**
- Trouver un **optimum** (max, min, moyenne) sur une fenêtre
- **Contraintes de taille** de fenêtre (fixe ou variable)
- Optimiser des algorithmes de **force brute O(n²)** vers O(n)

## 🔍 Comment identifier ce pattern

**Mots-clés dans l'énoncé :**
- "sous-tableau", "sous-chaîne"
- "fenêtre de taille k"
- "maximum/minimum dans une fenêtre"
- "au plus k caractères différents"
- "contiguous", "consecutive"

## 📋 Templates de base

### Template 1 - Fenêtre fixe

```go
func slidingWindowFixed(arr []int, k int) int {
    windowSum := 0
    
    // Construire la première fenêtre
    for i := 0; i < k; i++ {
        windowSum += arr[i]
    }
    
    maxSum := windowSum
    
    // Faire glisser la fenêtre
    for i := k; i < len(arr); i++ {
        windowSum += arr[i] - arr[i-k] // Ajouter nouveau, enlever ancien
        maxSum = max(maxSum, windowSum)
    }
    
    return maxSum
}
```

### Template 2 - Fenêtre variable

```go
func slidingWindowVariable(s string) int {
    left := 0
    maxLength := 0
    
    for right := 0; right < len(s); right++ {
        // Étendre la fenêtre
        // ... ajouter s[right] à la fenêtre
        
        // Contracter la fenêtre si nécessaire
        for windowInvalid {
            // ... enlever s[left] de la fenêtre
            left++
        }
        
        // Mettre à jour le résultat
        maxLength = max(maxLength, right-left+1)
    }
    
    return maxLength
}
```

## 🚀 Exemple 1 - Maximum Average Subarray (Fenêtre fixe)

**Problème :** Trouver la moyenne maximale d'un sous-tableau de taille k.

```go
func findMaxAverage(nums []int, k int) float64 {
    // Calculer la somme de la première fenêtre
    windowSum := 0
    for i := 0; i < k; i++ {
        windowSum += nums[i]
    }
    
    maxSum := windowSum
    
    // Faire glisser la fenêtre
    for i := k; i < len(nums); i++ {
        windowSum += nums[i] - nums[i-k]
        maxSum = max(maxSum, windowSum)
    }
    
    return float64(maxSum) / float64(k)
}

func max(a, b int) int {
    if a > b { return a }
    return b
}
```

## 🚀 Exemple 2 - Longest Substring Without Repeating Characters

**Problème :** Trouver la longueur de la plus longue sous-chaîne sans caractères répétés.

```go
func lengthOfLongestSubstring(s string) int {
    charMap := make(map[byte]int) // Caractère -> dernière position vue
    left := 0
    maxLength := 0
    
    for right := 0; right < len(s); right++ {
        // Si le caractère est déjà dans la fenêtre
        if lastPos, exists := charMap[s[right]]; exists && lastPos >= left {
            left = lastPos + 1 // Déplacer left après la dernière occurrence
        }
        
        charMap[s[right]] = right
        maxLength = max(maxLength, right-left+1)
    }
    
    return maxLength
}
```

## 🚀 Exemple 3 - Minimum Window Substring

**Problème :** Trouver la plus petite fenêtre contenant tous les caractères de t.

```go
func minWindow(s string, t string) string {
    if len(s) < len(t) {
        return ""
    }
    
    // Compter les caractères nécessaires
    need := make(map[byte]int)
    for i := 0; i < len(t); i++ {
        need[t[i]]++
    }
    
    needCount := len(need) // Nombre de caractères uniques nécessaires
    formed := 0            // Nombre de caractères uniques formés
    
    window := make(map[byte]int)
    left := 0
    
    // Résultat
    minLen := len(s) + 1
    minStart := 0
    
    for right := 0; right < len(s); right++ {
        // Étendre la fenêtre
        char := s[right]
        window[char]++
        
        if count, exists := need[char]; exists && window[char] == count {
            formed++
        }
        
        // Contracter la fenêtre
        for left <= right && formed == needCount {
            char = s[left]
            
            // Mettre à jour le résultat si nécessaire
            if right-left+1 < minLen {
                minLen = right - left + 1
                minStart = left
            }
            
            // Retirer le caractère de gauche
            window[char]--
            if count, exists := need[char]; exists && window[char] < count {
                formed--
            }
            
            left++
        }
    }
    
    if minLen == len(s)+1 {
        return ""
    }
    
    return s[minStart : minStart+minLen]
}
```

## 📊 Complexité

- **Fenêtre fixe :** O(n) temps, O(1) espace
- **Fenêtre variable :** O(n) temps, O(k) espace (k = taille alphabet)

## ⚠️ Pièges courants

1. **Indices hors limites** - Vérifier les bornes des pointeurs
2. **Fenêtre vide** - Gérer le cas left > right
3. **Mise à jour du résultat** - Bien choisir quand mettre à jour
4. **Map vs Array** - Utiliser array pour ASCII, map pour Unicode

## 🔄 Variations du pattern

### 1. Fenêtre fixe
- Taille de fenêtre constante k
- Template avec somme glissante

### 2. Fenêtre variable - Longest
- Étendre tant que valide
- Contracter quand invalide
- Mettre à jour le maximum

### 3. Fenêtre variable - Shortest  
- Étendre jusqu'à satisfaction
- Contracter au maximum
- Mettre à jour le minimum

## 🏃‍♂️ Exercices pratiques

### Easy
- [Maximum Average Subarray](../exercises/sliding-window/easy/max-average.md)
- [Contains Duplicate II](../exercises/sliding-window/easy/contains-duplicate.md)

### Medium
- [Longest Substring Without Repeating](../exercises/sliding-window/medium/longest-substring.md)
- [Max Consecutive Ones III](../exercises/sliding-window/medium/max-ones.md)
- [Fruit Into Baskets](../exercises/sliding-window/medium/fruit-baskets.md)

### Hard
- [Minimum Window Substring](../exercises/sliding-window/hard/min-window.md)
- [Sliding Window Maximum](../exercises/sliding-window/hard/sliding-max.md)

## ➡️ Cours suivant

[Cours 3 - Binary Search Pattern](03-binary-search.md) - Pour la recherche efficace dans les espaces ordonnés.
