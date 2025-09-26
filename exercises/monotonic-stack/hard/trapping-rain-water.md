# Exercice - Trapping Rain Water

**Niveau :** Hard
**Pattern :** Monotonic Stack
**Retour au cours :** [Monotonic Stack Pattern](../../courses/04-stack-monotonic.md)

## 📝 Énoncé

Étant donné `n` entiers non négatifs représentant un graphique d'élévation où la largeur de chaque barre est 1, calculez la quantité d'eau qu'il est possible de piéger après la pluie.

## 🔍 Exemples

**Exemple 1 :**
```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explication: Le graphique ci-dessus est représenté par le tableau [0,1,0,2,1,0,1,3,2,1,2,1].
Dans ce cas, 6 unités d'eau peuvent être piégées.
```

```
Visualisation:
   3         █
   2     █   ███ █
   1 █ █ ███████████
   0 ██████████████████
     01234567890123456

Eau piégée (W = water):
   3         █
   2     █WWW███ █
   1 █W█W███████████
   0 ██████████████████
```

**Exemple 2 :**
```
Input: height = [4,2,0,3,2,5]
Output: 9
```

```
Visualisation:
   5           █
   4 █         █
   3 █   █     █
   2 ███ █ █   █
   1 █████ █   █
   0 █████████████

Eau piégée:
   5           █
   4 █         █
   3 █WWW█     █
   2 ███W█W█   █
   1 █████W█   █
   0 █████████████
```

**Exemple 3 :**
```
Input: height = [3,0,2,0,4]
Output: 7
```

## 🎯 Contraintes

- `n == height.length`
- `1 <= n <= 2 * 10^4`
- `0 <= height[i] <= 10^5`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Il existe plusieurs approches : force brute O(n²), two pointers O(n), monotonic stack O(n), et DP O(n). Concentrez-vous sur l'approche monotonic stack.

</details>

<details>
<summary>Indice 2</summary>

Utilisez une stack décroissante qui stocke les indices. Quand vous trouvez une barre plus haute, elle peut former un "basin" avec la barre du bas de la stack.

</details>

<details>
<summary>Indice 3</summary>

Lors du pop, calculez l'eau piégée entre trois barres : current (droite), stack_top (bas), et nouvelle stack_top (gauche). L'eau forme un rectangle.

</details>

<details>
<summary>Indice 4</summary>

Volume d'eau = `min(height_left, height_right) - height_bottom) × width` où width est la distance entre les barres gauche et droite.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func trap(height []int) int {
    // Votre code ici

}

func main() {
    // Test cases
    fmt.Println(trap([]int{0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1}))
    // Expected: 6

    fmt.Println(trap([]int{4, 2, 0, 3, 2, 5}))
    // Expected: 9

    fmt.Println(trap([]int{3, 0, 2, 0, 4}))
    // Expected: 7

    fmt.Println(trap([]int{0}))
    // Expected: 0

    fmt.Println(trap([]int{1, 2, 3}))
    // Expected: 0

    fmt.Println(trap([]int{3, 2, 1}))
    // Expected: 0

    fmt.Println(trap([]int{2, 0, 2}))
    // Expected: 2

    fmt.Println(trap([]int{5, 4, 1, 2}))
    // Expected: 1

    fmt.Println(trap([]int{1, 0, 1, 2, 0, 3}))
    // Expected: 4
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func trap(height []int) int {
    if len(height) <= 2 {
        return 0
    }

    stack := []int{}  // Stack décroissante d'indices
    water := 0

    for i := 0; i < len(height); i++ {
        // Tant qu'on trouve une barre plus haute, calculer l'eau piégée
        for len(stack) > 0 && height[i] > height[stack[len(stack)-1]] {
            // La barre qui va être poppée forme le "bas" du basin
            bottom := stack[len(stack)-1]
            stack = stack[:len(stack)-1]

            // Si plus de barre à gauche, pas de basin possible
            if len(stack) == 0 {
                break
            }

            // Calculer les dimensions du basin
            left := stack[len(stack)-1]    // Barre gauche (mur gauche)
            right := i                     // Barre droite (mur droit)

            // Largeur du basin (exclut les murs)
            width := right - left - 1

            // Hauteur de l'eau = hauteur du mur le plus bas - hauteur du fond
            minHeight := min(height[left], height[right])
            waterHeight := minHeight - height[bottom]

            // Ajouter le volume d'eau
            water += waterHeight * width
        }

        // Push l'indice actuel
        stack = append(stack, i)
    }

    return water
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}
```

**Simulation détaillée pour `[3,0,2,0,4]` :**

```
i=0, height[0]=3: stack=[] → push 0 → stack=[0]
i=1, height[1]=0: stack=[0], 3>0 → push 1 → stack=[0,1]
i=2, height[2]=2: stack=[0,1], 0<2
    → pop 1 (bottom): height[bottom]=0
    → left=0, right=2, width=2-0-1=1
    → waterHeight=min(3,2)-0=2, water+=2×1=2
    → stack=[0], 3>2 → push 2 → stack=[0,2]

i=3, height[3]=0: stack=[0,2], 2>0 → push 3 → stack=[0,2,3]
i=4, height[4]=4: stack=[0,2,3], 0<4
    → pop 3: height[3]=0, left=2, right=4, width=4-2-1=1
    → waterHeight=min(2,4)-0=2, water+=2×1=4
    → stack=[0,2], 2<4
    → pop 2: height[2]=2, left=0, right=4, width=4-0-1=3
    → waterHeight=min(3,4)-2=1, water+=1×3=7
    → stack=[0], 3<4 → push 4 → stack=[0,4]

Résultat final: 7
```

**Visualisation de la simulation :**
```
Étapes de calcul pour [3,0,2,0,4]:

Étape 1 (i=2): Basin entre indices 0 et 2, fond à l'indice 1
   3     4
   █     █
   █  █  █
   █WW█  █  ← 2 unités d'eau
   █████████
   0 1 2 3 4

Étape 2 (i=4): Basin entre indices 2 et 4, fond à l'indice 3
   3     4
   █     █
   █  █  █
   █  █WW█  ← 2 unités d'eau supplémentaires
   █████████
   0 1 2 3 4

Étape 3 (i=4): Basin entre indices 0 et 4, fond à l'indice 2
   3     4
   █     █
   █W █  █
   █WW█WW█  ← 3 unités d'eau supplémentaires
   █████████
   0 1 2 3 4

Total: 2 + 2 + 3 = 7
```

**Explication de l'algorithme :**

1. **Stack décroissante** : maintient les indices des barres en ordre décroissant de hauteur
2. **Trigger de calcul** : quand `height[i] > height[stack_top]`, un basin peut se former
3. **Trois points critiques** : left (mur gauche), bottom (fond), right (mur droit)
4. **Volume du basin** : `(min(height_left, height_right) - height_bottom) × width`

**Complexité :**
- Temps : O(n) - chaque élément est pushé et poppé au plus une fois
- Espace : O(n) - stack dans le pire cas

**Intuition clé :**
- Chaque "concavité" peut piéger de l'eau si elle a des murs de chaque côté
- La stack maintient les barres potentielles qui peuvent former le fond d'un basin
- Quand on trouve une barre plus haute, elle "ferme" les basins en attente

</details>

## 🎯 Points clés à retenir

1. **Stack décroissante** : maintient les indices des barres candidates pour former un fond
2. **Calcul en couches** : l'eau peut être empilée en plusieurs niveaux
3. **Trois éléments** : besoin de left (mur), bottom (fond), right (mur) pour former un basin
4. **Pattern différent** : ici la stack aide à identifier des "concavités" plutôt que des monotonies

## 🚀 Exercices similaires

- [Largest Rectangle in Histogram](../medium/largest-rectangle.md) - Autre problème géométrique avec stack
- [Maximal Rectangle](./largest-rectangle-matrix.md) - Extension 2D des rectangles
- [Container With Most Water](../medium/container-most-water.md) - Problème similaire avec two pointers

## 🔍 Variations

**Version Two Pointers (plus intuitive) :**
```go
func trapTwoPointers(height []int) int {
    left, right := 0, len(height)-1
    leftMax, rightMax := 0, 0
    water := 0

    for left < right {
        if height[left] < height[right] {
            if height[left] >= leftMax {
                leftMax = height[left]
            } else {
                water += leftMax - height[left]
            }
            left++
        } else {
            if height[right] >= rightMax {
                rightMax = height[right]
            } else {
                water += rightMax - height[right]
            }
            right--
        }
    }

    return water
}
```

**Version DP (Dynamic Programming) :**
```go
func trapDP(height []int) int {
    n := len(height)
    if n <= 2 {
        return 0
    }

    leftMax := make([]int, n)
    rightMax := make([]int, n)

    leftMax[0] = height[0]
    for i := 1; i < n; i++ {
        leftMax[i] = max(leftMax[i-1], height[i])
    }

    rightMax[n-1] = height[n-1]
    for i := n - 2; i >= 0; i-- {
        rightMax[i] = max(rightMax[i+1], height[i])
    }

    water := 0
    for i := 0; i < n; i++ {
        water += min(leftMax[i], rightMax[i]) - height[i]
    }

    return water
}
```