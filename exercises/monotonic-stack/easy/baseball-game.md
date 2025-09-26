# Exercice - Baseball Game

**Niveau :** Easy
**Pattern :** Monotonic Stack
**Retour au cours :** [Monotonic Stack Pattern](../../courses/04-stack-monotonic.md)

## 📝 Énoncé

Vous gardez le score d'un match de baseball avec des règles étranges. Au début du jeu, vous commencez avec un record vide.

Vous recevez une liste d'opérations sous forme de chaînes `operations`, où `operations[i]` peut être l'un des suivants :

- Un **entier** `x` : enregistre un nouveau score de `x`
- `"+"` : enregistre un nouveau score qui est la somme des deux scores précédents
- `"D"` : enregistre un nouveau score qui est le double du score précédent
- `"C"` : invalide le score précédent, le supprimant du record

Retournez la somme de tous les scores du record après avoir appliqué toutes les opérations.

## 🔍 Exemples

**Exemple 1 :**
```
Input: ops = ["5","2","C","D","+"]
Output: 30
Explication:
"5" : record est [5]
"2" : record est [5, 2]
"C" : record est [5] (supprime le 2)
"D" : record est [5, 10] (double de 5)
"+" : record est [5, 10, 15] (somme de 5 + 10)
Total : 5 + 10 + 15 = 30
```

**Exemple 2 :**
```
Input: ops = ["5","-2","4","C","D","9","+","+"]
Output: 27
Explication:
"5" : record est [5]
"-2" : record est [5, -2]
"4" : record est [5, -2, 4]
"C" : record est [5, -2] (supprime le 4)
"D" : record est [5, -2, -4] (double de -2)
"9" : record est [5, -2, -4, 9]
"+" : record est [5, -2, -4, 9, 5] (somme de -4 + 9)
"+" : record est [5, -2, -4, 9, 5, 14] (somme de 9 + 5)
Total : 5 + (-2) + (-4) + 9 + 5 + 14 = 27
```

**Exemple 3 :**
```
Input: ops = ["1","C"]
Output: 0
Explication:
"1" : record est [1]
"C" : record est [] (supprime le 1)
Total : 0
```

## 🎯 Contraintes

- `1 <= operations.length <= 1000`
- `operations[i]` est `"C"`, `"D"`, `"+"`, ou une chaîne représentant un entier dans la plage `[-3 * 10^4, 3 * 10^4]`
- Pour l'opération `"+"`, il y aura toujours au moins deux scores précédents dans le record
- Pour les opérations `"C"` et `"D"`, il y aura toujours au moins un score précédent dans le record

## 💡 Indices

<details>
<summary>Indice 1</summary>

Une stack est parfaite pour ce problème car on a besoin d'accéder et modifier les derniers éléments ajoutés.

</details>

<details>
<summary>Indice 2</summary>

Pour chaque opération, utilisez la stack pour :
- Entier : push le score
- "+" : push la somme des deux derniers scores
- "D" : push le double du dernier score
- "C" : pop le dernier score

</details>

<details>
<summary>Indice 3</summary>

Convertissez les chaînes numériques en entiers avec `strconv.Atoi()` en Go.

</details>

<details>
<summary>Indice 4</summary>

À la fin, calculez la somme de tous les éléments restants dans la stack.

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "strconv"
)

func calPoints(operations []string) int {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println(calPoints([]string{"5", "2", "C", "D", "+"}))
    // Expected: 30

    fmt.Println(calPoints([]string{"5", "-2", "4", "C", "D", "9", "+", "+"}))
    // Expected: 27

    fmt.Println(calPoints([]string{"1", "C"}))
    // Expected: 0

    fmt.Println(calPoints([]string{"1"}))
    // Expected: 1

    fmt.Println(calPoints([]string{"3", "5", "D", "+", "C"}))
    // Expected: 13

    fmt.Println(calPoints([]string{"-1", "D", "+", "C", "C"}))
    // Expected: -1

    fmt.Println(calPoints([]string{"36", "28", "70", "65", "C", "+", "33", "-46", "84", "C"}))
    // Expected: 170

    fmt.Println(calPoints([]string{"1", "2", "+", "D", "C"}))
    // Expected: 3
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func calPoints(operations []string) int {
    stack := []int{} // Stack pour maintenir les scores valides

    for _, op := range operations {
        switch op {
        case "+":
            // Somme des deux derniers scores
            n := len(stack)
            newScore := stack[n-1] + stack[n-2]
            stack = append(stack, newScore)

        case "D":
            // Double du dernier score
            lastScore := stack[len(stack)-1]
            stack = append(stack, lastScore*2)

        case "C":
            // Supprime le dernier score
            stack = stack[:len(stack)-1]

        default:
            // C'est un entier, convertir et ajouter
            score, _ := strconv.Atoi(op)
            stack = append(stack, score)
        }
    }

    // Calculer la somme totale
    total := 0
    for _, score := range stack {
        total += score
    }

    return total
}
```

**Simulation détaillée pour `["5","2","C","D","+"]` :**

```
op="5": entier → stack=[5]
op="2": entier → stack=[5,2]
op="C": cancel → stack=[5] (supprime 2)
op="D": double → stack=[5,10] (double de 5)
op="+": somme → stack=[5,10,15] (5+10=15)

Somme finale: 5 + 10 + 15 = 30
```

**Simulation détaillée pour `["5","-2","4","C","D","9","+","+"]` :**

```
op="5": entier → stack=[5]
op="-2": entier → stack=[5,-2]
op="4": entier → stack=[5,-2,4]
op="C": cancel → stack=[5,-2] (supprime 4)
op="D": double → stack=[5,-2,-4] (double de -2)
op="9": entier → stack=[5,-2,-4,9]
op="+": somme → stack=[5,-2,-4,9,5] (-4+9=5)
op="+": somme → stack=[5,-2,-4,9,5,14] (9+5=14)

Somme finale: 5 + (-2) + (-4) + 9 + 5 + 14 = 27
```

**Explication de l'algorithme :**

1. **Stack simple** : maintient les scores valides du record
2. **Switch sur opérations** : traite chaque type d'opération
3. **Accès aux derniers éléments** : `stack[len(stack)-1]` pour le dernier, `stack[len(stack)-2]` pour l'avant-dernier
4. **Somme finale** : additionne tous les scores restants

**Complexité :**
- Temps : O(n) où n = nombre d'opérations
- Espace : O(n) pour la stack dans le pire cas

**Pourquoi une stack :**
- **Accès LIFO** : on a besoin des derniers scores pour les opérations "+", "D", "C"
- **Opérations efficaces** : push/pop en O(1)
- **État simple** : la stack représente exactement l'état actuel du record

</details>

## 🎯 Points clés à retenir

1. **Stack pour LIFO** : parfait pour accéder et modifier les derniers éléments
2. **Switch statement** : traite proprement les différents types d'opérations
3. **Conversion de types** : `strconv.Atoi()` pour convertir string en int
4. **Pas de monotonic** : contrairement aux autres exercices, ici on utilise juste une stack simple

## 🚀 Exercices similaires

- [Valid Parentheses](../easy/valid-parentheses.md) - Autre usage de stack simple
- [Next Greater Element I](./next-greater-i.md) - Usage de monotonic stack
- [Daily Temperatures](../medium/daily-temperatures.md) - Stack avec indices

## 🔍 Variations

**Version avec validation :**
```go
// Vérifier qu'il y a assez d'éléments avant "+" ou "D"
if op == "+" && len(stack) < 2 {
    return -1 // Erreur
}
if (op == "D" || op == "C") && len(stack) < 1 {
    return -1 // Erreur
}
```

**Version avec historique :**
```go
// Garder trace de toutes les opérations pour debugging
history := [][]int{}
history = append(history, append([]int{}, stack...))
```