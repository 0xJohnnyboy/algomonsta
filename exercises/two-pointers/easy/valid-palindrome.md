# Exercice - Palindrome Valide

**Niveau :** Easy
**Pattern :** Two Pointers
**Retour :** [📚 Cours Two Pointers](../../courses/01-two-pointers.md)

## 📝 Énoncé

Une phrase est un palindrome si, après avoir converti toutes les lettres majuscules en minuscules et supprimé tous les caractères non alphanumériques, elle se lit de la même façon vers l'avant et vers l'arrière. Les caractères alphanumériques incluent les lettres et les chiffres.

Étant donné une chaîne `s`, retournez `true` si c'est un palindrome, `false` sinon.

## 🔍 Exemples

**Exemple 1 :**
```
Input: s = "A man, a plan, a canal: Panama"
Output: true
Explication: "amanaplanacanalpanama" est un palindrome.
```

**Exemple 2 :**
```
Input: s = "race a car"
Output: false
Explication: "raceacar" n'est pas un palindrome.
```

**Exemple 3 :**
```
Input: s = " "
Output: true
Explication: s est une chaîne vide "" après suppression des caractères non alphanumériques.
Puisqu'une chaîne vide se lit de la même façon vers l'avant et vers l'arrière, c'est un palindrome.
```

## 🎯 Contraintes

- `1 <= s.length <= 2 * 10^5`
- `s` ne contient que des caractères ASCII imprimables.

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez deux pointeurs, un au début et un à la fin de la chaîne. Rapprochez-les l'un de l'autre en vérifiant que les caractères alphanumériques correspondent.

</details>

<details>
<summary>Indice 2</summary>

Ignorez les caractères non alphanumériques en déplaçant les pointeurs jusqu'à trouver des caractères valides à comparer.

</details>

<details>
<summary>Indice 3</summary>

Convertissez les caractères en minuscules avant de les comparer pour ignorer la casse.

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "strings"
    "unicode"
)

func isPalindrome(s string) bool {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        input    string
        expected bool
    }{
        {"A man, a plan, a canal: Panama", true},
        {"race a car", false},
        {" ", true},
        {"", true},
        {"a", true},
        {"Madam", true},
        {"No 'x' in Nixon", true},
        {"Mr. Owl ate my metal worm", true},
    }

    fmt.Println("=== Tests Palindrome Valide ===")
    for i, test := range tests {
        result := isPalindrome(test.input)
        status := "✅"
        if result != test.expected {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: %q\n", test.input)
        fmt.Printf("  Expected: %v, Got: %v\n\n", test.expected, result)
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func isPalindrome(s string) bool {
    left, right := 0, len(s)-1

    for left < right {
        // Ignorer les caractères non alphanumériques à gauche
        for left < right && !unicode.IsLetter(rune(s[left])) && !unicode.IsDigit(rune(s[left])) {
            left++
        }

        // Ignorer les caractères non alphanumériques à droite
        for left < right && !unicode.IsLetter(rune(s[right])) && !unicode.IsDigit(rune(s[right])) {
            right--
        }

        // Comparer les caractères en minuscules
        if strings.ToLower(string(s[left])) != strings.ToLower(string(s[right])) {
            return false
        }

        left++
        right--
    }

    return true
}
```

**Explication :**
1. Utilisez deux pointeurs `left` et `right` aux extrémités de la chaîne
2. Déplacez les pointeurs vers l'intérieur en ignorant les caractères non alphanumériques
3. Comparez les caractères en minuscules
4. Si tous les caractères correspondent, c'est un palindrome

**Complexité :**
- Temps : O(n) - un seul passage à travers la chaîne
- Espace : O(1) - seuls quelques pointeurs utilisés

</details>

## 🚀 Exercices similaires

- [Two Sum II](two-sum-ii.md)
- [Remove Duplicates](remove-duplicates.md)
- [Three Sum](../medium/three-sum.md)