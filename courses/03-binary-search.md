# Cours 3 - Binary Search Pattern

## 🎯 Quand utiliser ce pattern ?

Le pattern **Binary Search** est essentiel pour :
- **Recherche dans un espace trié** (tableaux, matrices)
- **Optimisation** : trouver le minimum/maximum satisfaisant une condition
- **Réduction de complexité** O(log n) au lieu de O(n)
- **Problèmes de "guess and check"** avec propriété monotone

## 🔍 Comment identifier ce pattern

**Mots-clés dans l'énoncé :**
- "tableau trié", "ordre croissant/décroissant"
- "find target", "search for"
- "minimum/maximum value such that..."
- "smallest/largest index where..."
- "peak element", "rotation point"

**Signaux moins évidents :**
- "Can we do better than O(n)?" sur un problème de recherche
- Contraintes de temps strictes avec grands datasets
- Problèmes d'optimisation avec fonction monotone

## 📋 Templates de base

### Template 1 - Binary Search classique

```go
func binarySearch(nums []int, target int) int {
    left, right := 0, len(nums)-1
    
    for left <= right {
        mid := left + (right-left)/2 // Évite l'overflow
        
        if nums[mid] == target {
            return mid
        } else if nums[mid] < target {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }
    
    return -1 // Target non trouvé
}
```

### Template 2 - Find First/Last Position

```go
func findFirst(nums []int, target int) int {
    left, right := 0, len(nums)-1
    result := -1
    
    for left <= right {
        mid := left + (right-left)/2
        
        if nums[mid] == target {
            result = mid
            right = mid - 1 // Continuer à chercher à gauche
        } else if nums[mid] < target {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }
    
    return result
}

func findLast(nums []int, target int) int {
    left, right := 0, len(nums)-1
    result := -1
    
    for left <= right {
        mid := left + (right-left)/2
        
        if nums[mid] == target {
            result = mid
            left = mid + 1 // Continuer à chercher à droite
        } else if nums[mid] < target {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }
    
    return result
}
```

### Template 3 - Binary Search on Answer Space

```go
func binarySearchAnswer(nums []int, target int) int {
    left, right := minPossible, maxPossible
    
    for left < right {
        mid := left + (right-left)/2
        
        if isValid(mid, nums, target) {
            right = mid // mid pourrait être la réponse
        } else {
            left = mid + 1
        }
    }
    
    return left
}

func isValid(candidate int, nums []int, target int) bool {
    // Logique pour vérifier si candidate est valide
    return true
}
```

## 🚀 Exemple 1 - Search in Rotated Sorted Array

**Problème :** Chercher dans un tableau trié puis rotationné.

```go
func search(nums []int, target int) int {
    left, right := 0, len(nums)-1
    
    for left <= right {
        mid := left + (right-left)/2
        
        if nums[mid] == target {
            return mid
        }
        
        // Déterminer quel côté est trié
        if nums[left] <= nums[mid] { // Côté gauche trié
            if nums[left] <= target && target < nums[mid] {
                right = mid - 1 // Target dans la partie triée
            } else {
                left = mid + 1 // Target dans la partie non-triée
            }
        } else { // Côté droit trié
            if nums[mid] < target && target <= nums[right] {
                left = mid + 1 // Target dans la partie triée
            } else {
                right = mid - 1 // Target dans la partie non-triée
            }
        }
    }
    
    return -1
}
```

## 🚀 Exemple 2 - Find Peak Element

**Problème :** Trouver un élément qui est plus grand que ses voisins.

```go
func findPeakElement(nums []int) int {
    left, right := 0, len(nums)-1
    
    for left < right {
        mid := left + (right-left)/2
        
        if nums[mid] > nums[mid+1] {
            // Peak est à gauche (ou mid est le peak)
            right = mid
        } else {
            // Peak est à droite
            left = mid + 1
        }
    }
    
    return left
}
```

## 🚀 Exemple 3 - Koko Eating Bananas (Binary Search on Answer)

**Problème :** Trouver la vitesse minimum pour manger toutes les bananes en h heures.

```go
import "math"

func minEatingSpeed(piles []int, h int) int {
    left, right := 1, getMax(piles)
    
    for left < right {
        mid := left + (right-left)/2
        
        if canFinish(piles, mid, h) {
            right = mid // mid pourrait être la réponse
        } else {
            left = mid + 1 // mid trop lent
        }
    }
    
    return left
}

func canFinish(piles []int, speed, h int) bool {
    totalTime := 0
    
    for _, pile := range piles {
        totalTime += int(math.Ceil(float64(pile) / float64(speed)))
    }
    
    return totalTime <= h
}

func getMax(arr []int) int {
    maxVal := arr[0]
    for _, val := range arr {
        if val > maxVal {
            maxVal = val
        }
    }
    return maxVal
}
```

## 📊 Complexité

- **Temporelle :** O(log n) pour la recherche, O(n log max) pour binary search on answer
- **Spatiale :** O(1) - recherche itérative

## ⚠️ Pièges courants

1. **Overflow d'entiers** - Utiliser `mid = left + (right-left)/2`
2. **Boucle infinie** - Bien gérer les conditions `left <= right` vs `left < right`
3. **Bornes incorrectes** - Attention aux `mid+1` et `mid-1`
4. **Cas particuliers** - Tableaux vides, un seul élément

## 🔄 Variations du pattern

### 1. Find Exact Match
- Recherche classique
- Retourne index ou -1

### 2. Find First/Last Occurrence
- Continuer la recherche après avoir trouvé
- Utile pour les doublons

### 3. Find Insertion Point
- `left` donne la position d'insertion après la boucle
- Équivalent à `bisect_left` en Python

### 4. Binary Search on Answer Space
- L'espace de recherche n'est pas un tableau
- Recherche de la réponse optimale

## 🎭 Patterns spécialisés

### Matrix Search (2D Binary Search)
```go
func searchMatrix(matrix [][]int, target int) bool {
    if len(matrix) == 0 || len(matrix[0]) == 0 {
        return false
    }
    
    rows, cols := len(matrix), len(matrix[0])
    left, right := 0, rows*cols-1
    
    for left <= right {
        mid := left + (right-left)/2
        row, col := mid/cols, mid%cols
        
        if matrix[row][col] == target {
            return true
        } else if matrix[row][col] < target {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }
    
    return false
}
```

## 🏃‍♂️ Exercices pratiques

### Easy
- [Binary Search](../exercises/binary-search/easy/binary-search.md)
- [Search Insert Position](../exercises/binary-search/easy/search-insert.md)
- [First Bad Version](../exercises/binary-search/easy/first-bad-version.md)

### Medium
- [Search in Rotated Sorted Array](../exercises/binary-search/medium/search-rotated.md)
- [Find Peak Element](../exercises/binary-search/medium/find-peak.md)
- [Koko Eating Bananas](../exercises/binary-search/medium/koko-bananas.md)
- [Search 2D Matrix](../exercises/binary-search/medium/search-matrix.md)

### Hard
- [Median of Two Sorted Arrays](../exercises/binary-search/hard/median-arrays.md)
- [Split Array Largest Sum](../exercises/binary-search/hard/split-array.md)

## 🧠 Stratégie de résolution

1. **Identifier si c'est du Binary Search**
   - Données triées ? Propriété monotone ?
   
2. **Choisir le bon template**
   - Recherche exacte ? First/Last ? Answer space ?
   
3. **Définir les bornes**
   - `left = 0, right = len-1` ou `left = 0, right = len` ?
   
4. **Implémenter la logique de comparaison**
   - Comment décider d'aller à gauche ou à droite ?
   
5. **Gérer les cas limites**
   - Tableau vide, un élément, pas de solution

## ➡️ Cours suivant

[Cours 4 - Monotonic Stack Pattern](04-stack-monotonic.md) - Pour maintenir un ordre spécifique avec une pile.
