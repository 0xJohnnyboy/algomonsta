# Exercice - Koko Eating Bananas

**Niveau :** Medium  
**Pattern :** Binary Search on Answer Space  
**Retour au cours :** [Binary Search Pattern](../../courses/03-binary-search.md)

## 📝 Énoncé

Koko adore manger des bananes. Il y a `n` piles de bananes, la `i`-ème pile a `piles[i]` bananes. Les gardiens sont partis et reviendront dans `h` heures.

Koko peut décider de sa **vitesse de consommation de bananes par heure** `k`. Chaque heure, elle choisit une pile de bananes et mange `k` bananes de cette pile. Si la pile a moins de `k` bananes, elle mange toutes les bananes de cette pile et ne mangera plus de bananes pendant cette heure.

Koko aime manger lentement mais veut finir de manger toutes les bananes avant le retour des gardiens.

Retournez **la vitesse de consommation minimale** `k` pour que Koko puisse manger toutes les bananes dans `h` heures.

## 🔍 Exemples

**Exemple 1 :**
```
Input: piles = [3,6,7,11], h = 8
Output: 4
Explication:
- Pile 1: 3 bananes → ⌈3/4⌉ = 1 heure
- Pile 2: 6 bananes → ⌈6/4⌉ = 2 heures  
- Pile 3: 7 bananes → ⌈7/4⌉ = 2 heures
- Pile 4: 11 bananes → ⌈11/4⌉ = 3 heures
Total: 1 + 2 + 2 + 3 = 8 heures
```

**Exemple 2 :**
```
Input: piles = [30,11,23,4,20], h = 5
Output: 30
Explication: Koko doit manger au moins 30 bananes/heure pour finir en 5h
```

**Exemple 3 :**
```
Input: piles = [30,11,23,4,20], h = 6  
Output: 23
```

## 🎯 Contraintes

- `1 <= piles.length <= 10^4`
- `piles.length <= h <= 10^9`
- `1 <= piles[i] <= 10^9`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème de "Binary Search on Answer Space". L'espace de recherche est la vitesse k, pas un tableau.

</details>

<details>
<summary>Indice 2</summary>

La vitesse minimale est 1, la vitesse maximale est max(piles) - pourquoi ?

</details>

<details>
<summary>Indice 3</summary>

Pour une vitesse donnée k, comment calculer le temps total nécessaire ? Utilisez ceiling division.

</details>

<details>
<summary>Indice 4</summary>

Si on peut finir avec une vitesse k, peut-on finir avec une vitesse k+1 ? Cette propriété monotone justifie le binary search.

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "math"
)

func minEatingSpeed(piles []int, h int) int {
    // Votre code ici
    
}

// Fonction helper pour calculer le temps nécessaire avec une vitesse donnée
func timeNeeded(piles []int, speed int) int {
    // Votre code ici
    
}

// Fonction helper pour trouver le maximum d'un tableau
func getMax(arr []int) int {
    // Votre code ici
    
}

func main() {
    // Test cases
    fmt.Println(minEatingSpeed([]int{3, 6, 7, 11}, 8))        // Expected: 4
    fmt.Println(minEatingSpeed([]int{30, 11, 23, 4, 20}, 5))  // Expected: 30
    fmt.Println(minEatingSpeed([]int{30, 11, 23, 4, 20}, 6))  // Expected: 23
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
import "math"

func minEatingSpeed(piles []int, h int) int {
    // Définir l'espace de recherche
    left, right := 1, getMax(piles)
    
    // Binary search sur la vitesse
    for left < right {
        mid := left + (right-left)/2
        
        if timeNeeded(piles, mid) <= h {
            // mid est assez rapide, essayer plus lent
            right = mid
        } else {
            // mid trop lent, augmenter la vitesse
            left = mid + 1
        }
    }
    
    return left
}

func timeNeeded(piles []int, speed int) int {
    totalTime := 0
    
    for _, pile := range piles {
        // Ceiling division: ⌈pile/speed⌉
        totalTime += int(math.Ceil(float64(pile) / float64(speed)))
    }
    
    return totalTime
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

**Explication détaillée :**

1. **Espace de recherche :** La vitesse k peut être entre 1 et max(piles)
   - Vitesse 1 : le plus lent possible
   - max(piles) : peut manger la plus grande pile en 1h

2. **Propriété monotone :** Si on peut finir avec vitesse k, on peut aussi finir avec k+1, k+2, etc.

3. **Binary Search :** On cherche la **plus petite** vitesse qui fonctionne
   - Si `timeNeeded(mid) <= h` → mid fonctionne, essayer plus lent
   - Sinon → mid trop lent, augmenter la vitesse

4. **Calcul du temps :** Pour chaque pile, le temps = ⌈pile/speed⌉

**Complexité :**
- Temps : O(n * log(max(piles))) où n = nombre de piles
- Espace : O(1)

**Points clés :**
- C'est du binary search sur l'espace des réponses, pas sur un tableau
- La fonction `timeNeeded` vérifie si une vitesse est valide
- Utilisation de ceiling division pour le calcul correct du temps

</details>

## 🚀 Exercices similaires

- [Split Array Largest Sum](../hard/split-array.md) - Même pattern, différent contexte
- [Capacity to Ship Packages](../medium/ship-packages.md) - Binary search sur capacité
- [Minimize Max Distance to Gas Station](../hard/gas-station.md) - Plus avancé

## 🎓 Concepts clés

- **Binary Search on Answer Space** : chercher dans l'espace des solutions
- **Propriété monotone** : si k fonctionne, k+1 fonctionne aussi  
- **Ceiling division** : `⌈a/b⌉ = (a + b - 1) / b` en entiers
- **Fonction de validation** : vérifier si une candidate solution est valide
