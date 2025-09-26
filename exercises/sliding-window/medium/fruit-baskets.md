# Exercice - Fruit Into Baskets

**Niveau :** Medium
**Pattern :** Sliding Window
**Retour :** [📚 Cours Sliding Window](../../courses/02-sliding-window.md)

## 📝 Énoncé

Vous visitez une ferme qui a une seule rangée d'arbres fruitiers disposés de gauche à droite. Les arbres sont représentés par un tableau d'entiers `fruits` où `fruits[i]` est le type de fruit que produit le `ième` arbre.

Vous voulez collecter autant de fruits que possible. Cependant, le propriétaire a quelques règles strictes que vous devez suivre :

- Vous ne disposez que de deux paniers, et chaque panier ne peut contenir qu'un seul type de fruit. Il n'y a pas de limite au nombre de fruits que chaque panier peut contenir.
- En commençant par n'importe quel arbre de votre choix, vous devez cueillir exactement un fruit de chaque arbre (y compris l'arbre de départ) en vous déplaçant vers la droite. Les fruits cueillis doivent correspondre à l'un des types de fruits de vos paniers.
- Une fois que vous atteignez un arbre dont le fruit ne peut pas être mis dans vos paniers, vous devez vous arrêter.

Étant donné le tableau d'entiers `fruits`, retournez le nombre maximum de fruits que vous pouvez cueillir.

## 🔍 Exemples

**Exemple 1 :**
```
Input: fruits = [1,2,1,2,2]
Output: 5
Explication: On peut cueillir de tous les arbres. Types: 1 et 2
```

**Exemple 2 :**
```
Input: fruits = [0,1,2,2]
Output: 3
Explication: On peut cueillir de [1,2,2]. Types: 1 et 2
```

**Exemple 3 :**
```
Input: fruits = [1,2,3,2,2]
Output: 4
Explication: On peut cueillir de [2,3,2,2]. Types: 2 et 3
```

## 🎯 Contraintes

- `1 <= fruits.length <= 10^5`
- `0 <= fruits[i] < fruits.length`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Ce problème est équivalent à trouver la plus longue sous-séquence contiguë avec au plus 2 types de fruits distincts.

</details>

<details>
<summary>Indice 2</summary>

Utilisez une fenêtre glissante avec une map pour compter les types de fruits dans la fenêtre courante.

</details>

<details>
<summary>Indice 3</summary>

Quand vous avez plus de 2 types distincts, rétrécissez la fenêtre depuis la gauche jusqu'à avoir exactement 2 types.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func totalFruit(fruits []int) int {
    // TODO: Implémentez votre solution ici
}

func main() {
    // Tests fournis
    tests := []struct {
        fruits   []int
        expected int
    }{
        {[]int{1, 2, 1, 2, 2}, 5},
        {[]int{0, 1, 2, 2}, 3},
        {[]int{1, 2, 3, 2, 2}, 4},
        {[]int{3, 3, 3, 1, 2, 1, 1, 2, 3, 3, 4}, 5},
        {[]int{1, 1, 1, 1}, 4},
        {[]int{1, 2, 3, 4, 5}, 2},
        {[]int{0, 1, 6, 6, 4, 4, 6}, 5},
        {[]int{1, 0, 1, 4, 1, 4, 1, 2, 3}, 6},
    }

    fmt.Println("=== Tests Fruit Into Baskets ===")
    for i, test := range tests {
        result := totalFruit(test.fruits)
        status := "✅"
        if result != test.expected {
            status = "❌"
        }
        fmt.Printf("Test %d: %s\n", i+1, status)
        fmt.Printf("  Input: fruits=%v\n", test.fruits)
        fmt.Printf("  Expected: %d, Got: %d\n\n", test.expected, result)
    }
}
```

## ✅ Solution

<details>
<summary>Voir la solution complète</summary>

```go
func totalFruit(fruits []int) int {
    left := 0
    maxFruits := 0
    fruitCount := make(map[int]int)

    for right := 0; right < len(fruits); right++ {
        // Ajouter le fruit courant à la fenêtre
        fruitCount[fruits[right]]++

        // Si on a plus de 2 types de fruits, rétrécir la fenêtre
        for len(fruitCount) > 2 {
            fruitCount[fruits[left]]--
            if fruitCount[fruits[left]] == 0 {
                delete(fruitCount, fruits[left])
            }
            left++
        }

        // Mettre à jour le maximum de fruits
        currentFruits := right - left + 1
        if currentFruits > maxFruits {
            maxFruits = currentFruits
        }
    }

    return maxFruits
}
```

**Explication :**
1. Utilisez une fenêtre glissante avec une map pour compter les types de fruits
2. Étendez la fenêtre vers la droite en ajoutant chaque fruit
3. Si la fenêtre contient plus de 2 types de fruits, rétrécissez depuis la gauche
4. Gardez la trace de la taille maximale de fenêtre valide (avec ≤ 2 types)
5. Cette approche garantit qu'on trouve la plus longue séquence avec au plus 2 types distincts

**Complexité :**
- Temps : O(n) - chaque élément est visité au plus deux fois
- Espace : O(1) - au maximum 3 entrées dans la map (avant nettoyage)

</details>

## 🚀 Exercices similaires

- [Longest Substring Without Repeating Characters](longest-substring.md)
- [Max Consecutive Ones III](max-ones.md)
- [Minimum Window Substring](../hard/min-window.md)