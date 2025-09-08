# Exercice - Longest Substring Without Repeating Characters

**Niveau :** Medium  
**Pattern :** Sliding Window (Fenêtre variable)  
**Retour au cours :** [Sliding Window Pattern](../../courses/02-sliding-window.md)

## 📝 Énoncé

Étant donné une chaîne de caractères `s`, trouvez la longueur de la **plus longue sous-chaîne** sans caractères répétés.

## 🔍 Exemples

**Exemple 1 :**
```
Input: s = "abcabcbb"
Output: 3
Explication: La réponse est "abc", avec une longueur de 3.
```

**Exemple 2 :**
```
Input: s = "bbbbb"
Output: 1
Explication: La réponse est "b", avec une longueur de 1.
```

**Exemple 3 :**
```
Input: s = "pwwkew"
Output: 3
Explication: La réponse est "wke", avec une longueur de 3.
Notez que la réponse doit être une sous-chaîne, "pwke" est une sous-séquence et non une sous-chaîne.
```

**Exemple 4 :**
```
Input: s = ""
Output: 0
```

**Exemple 5 :**
```
Input: s = " "
Output: 1
```

## 🎯 Contraintes

- `0 <= s.length <= 5 * 10^4`
- `s` consiste en lettres anglaises, chiffres, symboles et espaces.

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez une **fenêtre glissante variable**. Étendez la fenêtre vers la droite et contractez-la quand vous trouvez un caractère répété.

</details>

<details>
<summary>Indice 2</summary>

Maintenez une **Map/Set** pour suivre les caractères dans la fenêtre actuelle. Utilisez deux pointeurs `left` et `right`.

</details>

<details>
<summary>Indice 3</summary>

Quand vous trouvez un caractère répété à la position `right`, déplacez `left` pour **éliminer la première occurrence** de ce caractère.

</details>

<details>
<summary>Indice 4</summary>

**Optimisation :** Au lieu de déplacer `left` caractère par caractère, sautez directement après la dernière occurrence du caractère répété.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func lengthOfLongestSubstring(s string) int {
    // Votre code ici
    
}

func main() {
    // Test cases
    fmt.Println(lengthOfLongestSubstring("abcabcbb"))  // Expected: 3
    fmt.Println(lengthOfLongestSubstring("bbbbb"))     // Expected: 1
    fmt.Println(lengthOfLongestSubstring("pwwkew"))    // Expected: 3
    fmt.Println(lengthOfLongestSubstring(""))          // Expected: 0
    fmt.Println(lengthOfLongestSubstring(" "))         // Expected: 1
    fmt.Println(lengthOfLongestSubstring("au"))        // Expected: 2
    fmt.Println(lengthOfLongestSubstring("dvdf"))      // Expected: 3
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

### Solution 1 - Sliding Window avec Map (Optimisée)

```go
func lengthOfLongestSubstring(s string) int {
    charMap := make(map[byte]int) // caractère -> dernière position vue
    left := 0
    maxLength := 0
    
    for right := 0; right < len(s); right++ {
        char := s[right]
        
        // Si le caractère est déjà dans la fenêtre
        if lastPos, exists := charMap[char]; exists && lastPos >= left {
            left = lastPos + 1 // Déplacer left après la dernière occurrence
        }
        
        // Mettre à jour la position du caractère
        charMap[char] = right
        
        // Mettre à jour la longueur maximale
        maxLength = max(maxLength, right-left+1)
    }
    
    return maxLength
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```

### Solution 2 - Sliding Window avec Set (Plus claire)

```go
func lengthOfLongestSubstringSet(s string) int {
    charSet := make(map[byte]bool)
    left := 0
    maxLength := 0
    
    for right := 0; right < len(s); right++ {
        // Contracter la fenêtre jusqu'à éliminer le doublon
        for charSet[s[right]] {
            delete(charSet, s[left])
            left++
        }
        
        // Ajouter le caractère actuel
        charSet[s[right]] = true
        
        // Mettre à jour la longueur maximale
        maxLength = max(maxLength, right-left+1)
    }
    
    return maxLength
}
```

### Solution 3 - Avec Array (pour ASCII seulement)

```go
func lengthOfLongestSubstringArray(s string) int {
    // Pour caractères ASCII seulement (0-127)
    lastIndex := make([]int, 128)
    for i := range lastIndex {
        lastIndex[i] = -1
    }
    
    left := 0
    maxLength := 0
    
    for right := 0; right < len(s); right++ {
        char := s[right]
        
        // Si le caractère a été vu dans la fenêtre actuelle
        if lastIndex[char] >= left {
            left = lastIndex[char] + 1
        }
        
        lastIndex[char] = right
        maxLength = max(maxLength, right-left+1)
    }
    
    return maxLength
}
```

**Simulation détaillée pour s = "pwwkew" :**

```
Utilisation de la Solution 1 (Map optimisée):

right=0, char='p':
  charMap={}, left=0
  'p' pas dans map → charMap={'p': 0}
  maxLength = max(0, 0-0+1) = 1
  charMap={'p': 0}, left=0

right=1, char='w':
  'w' pas dans map → charMap={'p': 0, 'w': 1}
  maxLength = max(1, 1-0+1) = 2
  charMap={'p': 0, 'w': 1}, left=0

right=2, char='w':
  'w' dans map à position 1, 1 >= 0 → left = 1+1 = 2
  charMap={'p': 0, 'w': 2}
  maxLength = max(2, 2-2+1) = 2
  charMap={'p': 0, 'w': 2}, left=2

right=3, char='k':
  'k' pas dans map → charMap={'p': 0, 'w': 2, 'k': 3}
  maxLength = max(2, 3-2+1) = 2
  charMap={'p': 0, 'w': 2, 'k': 3}, left=2

right=4, char='e':
  'e' pas dans map → charMap={'p': 0, 'w': 2, 'k': 3, 'e': 4}
  maxLength = max(2, 4-2+1) = 3
  charMap={'p': 0, 'w': 2, 'k': 3, 'e': 4}, left=2

right=5, char='w':
  'w' dans map à position 2, 2 >= 2 → left = 2+1 = 3
  charMap={'p': 0, 'w': 5, 'k': 3, 'e': 4}
  maxLength = max(3, 5-3+1) = 3
  
Résultat: 3 (sous-chaîne "wke")
```

**Comparaison des solutions :**

| Solution | Avantages | Inconvénients | Complexité |
|----------|-----------|---------------|------------|
| **Map optimisée** | Plus efficace, gère Unicode | Logique plus complexe | O(n) temps, O(min(m,n)) espace |
| **Set simple** | Logic plus claire | Peut être plus lent | O(n) temps, O(min(m,n)) espace |
| **Array ASCII** | Le plus rapide pour ASCII | Limité à ASCII | O(n) temps, O(1) espace |

**Explication des approches :**

1. **Map optimisée :** Saute directement après la dernière occurrence du caractère répété
2. **Set simple :** Contracte la fenêtre caractère par caractère jusqu'à éliminer le doublon
3. **Array ASCII :** Comme la map mais avec un tableau pour les caractères ASCII

**Points clés de l'algorithme :**

1. **Fenêtre variable :** Étendre avec `right`, contracter avec `left`
2. **Condition d'invalidité :** Caractère répété dans la fenêtre
3. **Optimisation :** Sauter directement après la dernière occurrence
4. **Mise à jour :** Toujours mettre à jour la position du caractère

**Complexité :**
- **Temps :** O(n) - chaque caractère est visité au maximum 2 fois
- **Espace :** O(min(m, n)) où m = taille de l'alphabet, n = longueur de la chaîne

**Cas limites à considérer :**
- Chaîne vide : `""` → 0
- Un seul caractère : `"a"` → 1
- Tous caractères identiques : `"aaaa"` → 1
- Tous caractères différents : `"abcd"` → 4
- Caractères spéciaux et espaces

</details>

## 🎯 Points clés à retenir

1. **Sliding Window variable** : Étendre et contracter selon la validité
2. **Tracking des caractères** : Map pour position, Set pour présence
3. **Optimisation** : Sauter directement après le caractère répété
4. **Mise à jour continue** : Toujours mettre à jour maxLength

## 🚀 Exercices similaires

- [Longest Substring with At Most K Distinct Characters](../medium/k-distinct-chars.md)
- [Minimum Window Substring](../hard/min-window-substring.md)
- [Longest Repeating Character Replacement](../medium/char-replacement.md)
- [Permutation in String](../medium/permutation-string.md)

## 🔍 Variations

**Longest Substring with At Most 2 Distinct Characters :**
```go
func lengthOfLongestSubstringTwoDistinct(s string) int {
    charCount := make(map[byte]int)
    left := 0
    maxLength := 0
    
    for right := 0; right < len(s); right++ {
        charCount[s[right]]++
        
        // Contracter si plus de 2 caractères distincts
        for len(charCount) > 2 {
            charCount[s[left]]--
            if charCount[s[left]] == 0 {
                delete(charCount, s[left])
            }
            left++
        }
        
        maxLength = max(maxLength, right-left+1)
    }
    
    return maxLength
}
```

**Character Replacement (K remplacements autorisés) :**
```go
func characterReplacement(s string, k int) int {
    charCount := make(map[byte]int)
    left := 0
    maxCount := 0
    maxLength := 0
    
    for right := 0; right < len(s); right++ {
        charCount[s[right]]++
        maxCount = max(maxCount, charCount[s[right]])
        
        // Si la fenêtre nécessite plus de k remplacements
        if (right-left+1)-maxCount > k {
            charCount[s[left]]--
            left++
        }
        
        maxLength = max(maxLength, right-left+1)
    }
    
    return maxLength
}
```

## 🎓 Concepts Sliding Window illustrés

- ✅ **Fenêtre variable** - Taille change selon la validité
- ✅ **État de fenêtre** - Map/Set pour tracking des caractères
- ✅ **Condition d'invalidité** - Caractère répété
- ✅ **Optimisation** - Sauter plutôt que déplacer graduellement
- ✅ **Complexité O(n)** - Chaque caractère visité au maximum 2 fois
