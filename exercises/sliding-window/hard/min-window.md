# Exercice - Minimum Window Substring

**Niveau :** Hard
**Pattern :** Sliding Window
**Retour :** [📚 Cours Sliding Window](../../courses/02-sliding-window.md)

## 📝 Énoncé

Étant donné deux chaînes `s` et `t` d'une longueur `m` et `n` respectivement, retournez la fenêtre minimale dans `s` qui contiendra tous les caractères de `t`. Si une telle fenêtre n'existe pas, retournez la chaîne vide `""`.

Les cas de test seront générés de manière à ce que la réponse soit unique.

Une fenêtre est une sous-chaîne contiguë de `s`.

## 🔍 Exemples

**Exemple 1 :**
```
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
Explication: La fenêtre minimale qui contient "ABC" est "BANC".
```

**Exemple 2 :**
```
Input: s = "a", t = "a"
Output: "a"
Explication: La chaîne entière s est la fenêtre minimale.
```

**Exemple 3 :**
```
Input: s = "a", t = "aa"
Output: ""
Explication: Les deux 'a' de t ne peuvent pas être trouvés dans s.
```

## 🎯 Contraintes

- `m == s.length`
- `n == t.length`
- `1 <= m, n <= 10^5`
- `s` et `t` consistent en lettres anglaises majuscules et minuscules.

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez deux pointeurs et deux maps : une pour compter les caractères requis dans `t`, une pour compter les caractères dans la fenêtre courante.

</details>

<details>
<summary>Indice 2</summary>

Étendez la fenêtre vers la droite jusqu'à ce qu'elle contienne tous les caractères de `t`.

</details>

<details>
<summary>Indice 3</summary>

Une fois qu'une fenêtre valide est trouvée, essayez de la rétrécir depuis la gauche tout en maintenant sa validité.

</details>

<details>
<summary>Indice 4</summary>

Utilisez une variable `formed` pour compter combien de caractères uniques dans la fenêtre courante ont la fréquence souhaitée.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func minWindow(s string, t string) string {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        s        string
        t        string
        expected string
    }{
        {"ADOBECODEBANC", "ABC", "BANC"},
        {"a", "a", "a"},
        {"a", "aa", ""},
        {"ab", "b", "b"},
        {"abc", "cba", "abc"},
        {"ADOBECODEBANC", "AABC", "ADOBEC"},
        {"aa", "aa", "aa"},
        {"acbbaca", "aba", "baca"},
        {"cabwefgewcwaefgcf", "cae", "cwae"},
    }

    fmt.Println("=== Tests Minimum Window Substring ===")
    for i, test := range tests {
        result := minWindow(test.s, test.t)
        status := "✅"
        if result != test.expected {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: s=\"%s\", t=\"%s\"\n", test.s, test.t)
        fmt.Printf("  Expected: \"%s\", Got: \"%s\"\n\n", test.expected, result)
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func minWindow(s string, t string) string {
    if len(s) < len(t) {
        return ""
    }

    // Compter les caractères requis dans t
    tCount := make(map[byte]int)
    for i := 0; i < len(t); i++ {
        tCount[t[i]]++
    }

    required := len(tCount)
    formed := 0
    windowCount := make(map[byte]int)

    left := 0
    minLen := len(s) + 1
    minLeft := 0

    for right := 0; right < len(s); right++ {
        // Ajouter le caractère de droite à la fenêtre
        char := s[right]
        windowCount[char]++

        // Si la fréquence du caractère courant atteint la fréquence désirée
        if count, exists := tCount[char]; exists && windowCount[char] == count {
            formed++
        }

        // Essayer de contracter la fenêtre tant qu'elle reste "désirable"
        for left <= right && formed == required {
            // Sauvegarder la plus petite fenêtre jusqu'à présent
            if right-left+1 < minLen {
                minLen = right - left + 1
                minLeft = left
            }

            // Le caractère à la position gauche n'est plus une partie de la fenêtre
            leftChar := s[left]
            windowCount[leftChar]--
            if count, exists := tCount[leftChar]; exists && windowCount[leftChar] < count {
                formed--
            }

            left++
        }
    }

    if minLen == len(s)+1 {
        return ""
    }

    return s[minLeft : minLeft+minLen]
}
```

**Explication :**
1. Comptez la fréquence de chaque caractère dans `t`
2. Utilisez deux pointeurs pour maintenir une fenêtre glissante
3. Étendez la fenêtre vers la droite jusqu'à ce qu'elle contienne tous les caractères requis
4. Une fois une fenêtre valide trouvée, essayez de la rétrécir depuis la gauche
5. Gardez la trace de la plus petite fenêtre valide trouvée
6. Utilisez `formed` pour optimiser : comptez combien de caractères uniques ont la bonne fréquence

**Complexité :**
- Temps : O(|s| + |t|) - chaque caractère de s est visité au plus deux fois
- Espace : O(|s| + |t|) - pour les maps de comptage

</details>

## 🚀 Exercices similaires

- [Longest Substring Without Repeating Characters](../medium/longest-substring.md)
- [Sliding Window Maximum](sliding-max.md)
- [Fruit Into Baskets](../medium/fruit-baskets.md)