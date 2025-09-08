# Cours 4 - Monotonic Stack Pattern

## 🎯 Quand utiliser ce pattern ?

La **Monotonic Stack** est parfaite pour :
- Trouver le **prochain élément plus grand/petit** à droite ou à gauche
- Problèmes de **visibilité** (buildings, temperatures)
- **Calculs d'aires** (histogrammes, rectangles)
- **Optimisation O(n)** de problèmes qui semblent O(n²)

## 🔍 Comment identifier ce pattern

**Mots-clés dans l'énoncé :**
- "next greater element", "next smaller element"
- "previous greater/smaller"
- "largest rectangle", "maximum area"
- "daily temperatures", "stock span"
- "trapping rain water"

**Signaux :**
- Besoin de comparer chaque élément avec les éléments précédents/suivants
- Problèmes qui semblent nécessiter une boucle imbriquée O(n²)

## 🏗️ Concept fondamental

Une **Monotonic Stack** maintient ses éléments dans un ordre spécifique :
- **Monotonic Increasing** : les éléments sont en ordre croissant (du bas vers le haut)
- **Monotonic Decreasing** : les éléments sont en ordre décroissant (du bas vers le haut)

## 📋 Templates de base

### Template 1 - Next Greater Element

```go
func nextGreaterElement(nums []int) []int {
    n := len(nums)
    result := make([]int, n)
    stack := []int{} // Stack d'indices
    
    // Initialiser avec -1 (pas de plus grand trouvé)
    for i := range result {
        result[i] = -1
    }
    
    for i := 0; i < n; i++ {
        // Pop les éléments plus petits que nums[i]
        for len(stack) > 0 && nums[stack[len(stack)-1]] < nums[i] {
            index := stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            result[index] = nums[i]
        }
        
        // Push l'index actuel
        stack = append(stack, i)
    }
    
    return result
}
```

### Template 2 - Monotonic Increasing Stack

```go
func monotonicIncreasing(nums []int) []int {
    stack := []int{}
    
    for i, num := range nums {
        // Maintenir l'ordre croissant
        for len(stack) > 0 && nums[stack[len(stack)-1]] >= num {
            // Traiter l'élément poppé si nécessaire
            poppedIndex := stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            // Logique de traitement ici
        }
        
        stack = append(stack, i)
    }
    
    return stack
}
```

### Template 3 - Previous Smaller Element

```go
func previousSmallerElement(nums []int) []int {
    n := len(nums)
    result := make([]int, n)
    stack := []int{} // Stack d'indices
    
    for i := 0; i < n; i++ {
        // Pop les éléments >= nums[i]
        for len(stack) > 0 && nums[stack[len(stack)-1]] >= nums[i] {
            stack = stack[:len(stack)-1]
        }
        
        if len(stack) > 0 {
            result[i] = nums[stack[len(stack)-1]]
        } else {
            result[i] = -1 // Pas de plus petit à gauche
        }
        
        stack = append(stack, i)
    }
    
    return result
}
```

## 🚀 Exemple 1 - Daily Temperatures

**Problème :** Pour chaque jour, combien de jours attendre pour une température plus chaude ?

```go
func dailyTemperatures(temperatures []int) []int {
    n := len(temperatures)
    result := make([]int, n)
    stack := []int{} // Stack d'indices
    
    for i := 0; i < n; i++ {
        // Pop les jours avec température plus froide
        for len(stack) > 0 && temperatures[stack[len(stack)-1]] < temperatures[i] {
            prevDay := stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            result[prevDay] = i - prevDay // Nombre de jours à attendre
        }
        
        stack = append(stack, i)
    }
    
    // Les éléments restants dans la stack n'ont pas de jour plus chaud
    return result
}
```

## 🚀 Exemple 2 - Largest Rectangle in Histogram

**Problème :** Trouver l'aire du plus grand rectangle dans un histogramme.

```go
func largestRectangleArea(heights []int) int {
    stack := []int{} // Stack d'indices
    maxArea := 0
    
    for i := 0; i <= len(heights); i++ {
        // Ajouter une hauteur 0 à la fin pour forcer le pop de tous les éléments
        currentHeight := 0
        if i < len(heights) {
            currentHeight = heights[i]
        }
        
        // Pop les barres plus hautes
        for len(stack) > 0 && heights[stack[len(stack)-1]] > currentHeight {
            height := heights[stack[len(stack)-1]]
            stack = stack[:len(stack)-1]
            
            // Calculer la largeur
            width := i
            if len(stack) > 0 {
                width = i - stack[len(stack)-1] - 1
            }
            
            maxArea = max(maxArea, height*width)
        }
        
        stack = append(stack, i)
    }
    
    return maxArea
}

func max(a, b int) int {
    if a > b { return a }
    return b
}
```

## 🚀 Exemple 3 - Trapping Rain Water

**Problème :** Calculer la quantité d'eau de pluie qui peut être piégée.

```go
func trap(height []int) int {
    if len(height) == 0 {
        return 0
    }
    
    stack := []int{} // Stack d'indices
    waterTrapped := 0
    
    for i := 0; i < len(height); i++ {
        // Pop les barres plus basses et calculer l'eau piégée
        for len(stack) > 0 && height[i] > height[stack[len(stack)-1]] {
            bottom := stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            
            if len(stack) == 0 {
                break
            }
            
            // Calculer l'eau piégée entre stack[top] et i
            distance := i - stack[len(stack)-1] - 1
            boundedHeight := min(height[i], height[stack[len(stack)-1]]) - height[bottom]
            waterTrapped += distance * boundedHeight
        }
        
        stack = append(stack, i)
    }
    
    return waterTrapped
}

func min(a, b int) int {
    if a < b { return a }
    return b
}
```

## 📊 Complexité

- **Temporelle :** O(n) - chaque élément est pushé et poppé au maximum une fois
- **Spatiale :** O(n) - dans le pire cas, tous les éléments sont dans la stack

## ⚠️ Pièges courants

1. **Stack d'indices vs valeurs** - Généralement plus utile de stocker les indices
2. **Condition de pop** - Bien définir quand popper (`>`, `>=`, `<`, `<=`)
3. **Éléments restants** - Gérer les éléments qui restent dans la stack
4. **Sentinelle** - Parfois ajouter une valeur sentinelle simplifie le code

## 🔄 Variations du pattern

### 1. Next Greater Element
- Stack décroissante (monotonic decreasing)
- Pop quand élément actuel > top de la stack

### 2. Next Smaller Element  
- Stack croissante (monotonic increasing)
- Pop quand élément actuel < top de la stack

### 3. Previous Greater/Smaller
- Même logique mais on traite au moment du push

### 4. Circular Array
- Parcourir le tableau 2 fois avec modulo

## 🎭 Patterns avancés

### Stock Span Problem
```go
func stockSpan(prices []int) []int {
    n := len(prices)
    spans := make([]int, n)
    stack := []int{} // Stack d'indices
    
    for i := 0; i < n; i++ {
        // Pop les prix plus petits ou égaux
        for len(stack) > 0 && prices[stack[len(stack)-1]] <= prices[i] {
            stack = stack[:len(stack)-1]
        }
        
        if len(stack) == 0 {
            spans[i] = i + 1 // Tous les jours précédents
        } else {
            spans[i] = i - stack[len(stack)-1] // Jours depuis le dernier prix plus grand
        }
        
        stack = append(stack, i)
    }
    
    return spans
}
```

### Sum of Subarray Minimums
```go
func sumSubarrayMins(arr []int) int {
    const MOD = 1e9 + 7
    n := len(arr)
    
    // Calculer previous smaller et next smaller
    prevSmaller := make([]int, n)
    nextSmaller := make([]int, n)
    
    // Previous smaller elements
    stack := []int{}
    for i := 0; i < n; i++ {
        for len(stack) > 0 && arr[stack[len(stack)-1]] >= arr[i] {
            stack = stack[:len(stack)-1]
        }
        
        if len(stack) == 0 {
            prevSmaller[i] = -1
        } else {
            prevSmaller[i] = stack[len(stack)-1]
        }
        
        stack = append(stack, i)
    }
    
    // Next smaller elements
    stack = []int{}
    for i := n - 1; i >= 0; i-- {
        for len(stack) > 0 && arr[stack[len(stack)-1]] > arr[i] {
            stack = stack[:len(stack)-1]
        }
        
        if len(stack) == 0 {
            nextSmaller[i] = n
        } else {
            nextSmaller[i] = stack[len(stack)-1]
        }
        
        stack = append(stack, i)
    }
    
    // Calculer la somme
    result := 0
    for i := 0; i < n; i++ {
        left := i - prevSmaller[i]
        right := nextSmaller[i] - i
        result = (result + arr[i]*left*right) % MOD
    }
    
    return result
}
```

## 🏃‍♂️ Exercices pratiques

<!-- ### Easy -->
<!-- - [Next Greater Element I](../exercises/monotonic-stack/easy/next-greater-i.md) -->
<!-- - [Baseball Game](../exercises/monotonic-stack/easy/baseball-game.md) -->
<!---->
### Medium
- [Daily Temperatures](../exercises/monotonic-stack/medium/daily-temperatures.md)
<!-- - [Next Greater Element II](../exercises/monotonic-stack/medium/next-greater-ii.md) -->
<!-- - [Largest Rectangle in Histogram](../exercises/monotonic-stack/medium/largest-rectangle.md) -->
<!-- - [Sum of Subarray Minimums](../exercises/monotonic-stack/medium/subarray-minimums.md) -->
<!---->
<!-- ### Hard -->
<!-- - [Trapping Rain Water](../exercises/monotonic-stack/hard/trapping-rain-water.md) -->
<!-- - [Largest Rectangle in Binary Matrix](../exercises/monotonic-stack/hard/largest-rectangle-matrix.md) -->

## 🧠 Stratégie de résolution

1. **Identifier le besoin de comparaison** avec éléments précédents/suivants

2. **Déterminer le type de stack** nécessaire :
   - Next Greater → Stack décroissante
   - Next Smaller → Stack croissante
   - Previous Greater/Smaller → même logique

3. **Choisir entre indices et valeurs** :
   - Indices : plus flexible, permet calculs de distance
   - Valeurs : plus simple si seule la valeur importe

4. **Définir la condition de pop** précisément :
   - `>` vs `>=` peut changer complètement le résultat
   - Tester avec des exemples simples

5. **Gérer les éléments restants** dans la stack après la boucle

## 💡 Trucs et astuces

### Debugging
```go
// Ajouter des prints pour comprendre l'état de la stack
fmt.Printf("i=%d, num=%d, stack=%v\n", i, num, stack)
```

### Optimisations
- Utiliser `make([]int, 0, n)` pour préallouer la capacité
- Éviter les réallocations fréquentes

### Sentinelles
- Ajouter des valeurs sentinelles pour simplifier la logique
- Ex: hauteur 0 à la fin pour forcer le pop de tous les éléments

## ➡️ Cours suivant

[Cours 5 - DFS on Trees Pattern](05-dfs-tree.md) - Pour explorer les arbres en profondeur.
