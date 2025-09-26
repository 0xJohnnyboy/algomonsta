# Exercice - Binary Tree Maximum Path Sum

**Niveau :** Hard
**Pattern :** DFS on Trees (avec calcul global optimisé)
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

Un **chemin** dans un arbre binaire est une séquence de nœuds où chaque paire de nœuds adjacents dans la séquence a une arête les reliant. Un nœud peut apparaître au plus une fois dans la séquence. Notez qu'un chemin n'a pas besoin de passer par la racine.

La **somme d'un chemin** est la somme des valeurs des nœuds dans le chemin.

Étant donné la racine d'un arbre binaire, retournez la **somme maximale de n'importe quel chemin non vide**.

## 🔍 Exemples

**Exemple 1 :**
```
  1
 / \
2   3

Input: root = [1,2,3]
Output: 6
Explication: Le chemin optimal est 2 → 1 → 3 avec une somme de 2 + 1 + 3 = 6.
```

**Exemple 2 :**
```
    -10
    /  \
   9   20
      /  \
     15   7

Input: root = [-10,9,20,null,null,15,7]
Output: 42
Explication: Le chemin optimal est 15 → 20 → 7 avec une somme de 15 + 20 + 7 = 42.
```

**Exemple 3 :**
```
  -3

Input: root = [-3]
Output: -3
Explication: Le chemin est juste le nœud unique -3.
```

**Exemple 4 :**
```
    2
   /
 -1

Input: root = [2,-1]
Output: 2
Explication: Le chemin optimal est juste le nœud 2 (on ignore -1).
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[1, 3 * 10^4]`
- `-1000 <= Node.val <= 1000`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Pour chaque nœud, la somme maximale d'un chemin passant par ce nœud est : `node.val + max_gain(gauche) + max_gain(droite)`, où `max_gain` peut être 0 si le sous-arbre apporte une contribution négative.

</details>

<details>
<summary>Indice 2</summary>

Utilisez une approche **DFS** où chaque nœud retourne la contribution maximale qu'il peut apporter à son parent (gain d'un seul côté), tout en mettant à jour le maximum global.

</details>

<details>
<summary>Indice 3</summary>

Un nœud a deux rôles :
- **Comme point de passage** : somme incluant les deux sous-arbres
- **Comme contribution** : somme incluant un seul sous-arbre (pour son parent)

</details>

<details>
<summary>Indice 4</summary>

Si un sous-arbre apporte une contribution négative, il vaut mieux l'ignorer (gain = 0).

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "math"
)

type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

func maxPathSum(root *TreeNode) int {
    // Votre code ici

}

func main() {
    // Exemple 1: Somme positive
    //   1
    //  / \
    // 2   3
    root1 := &TreeNode{Val: 1}
    root1.Left = &TreeNode{Val: 2}
    root1.Right = &TreeNode{Val: 3}

    fmt.Println("Exemple 1 - somme max:", maxPathSum(root1))
    // Expected: 6

    // Exemple 2: Avec valeurs négatives
    //     -10
    //     /  \
    //    9   20
    //       /  \
    //      15   7
    root2 := &TreeNode{Val: -10}
    root2.Left = &TreeNode{Val: 9}
    root2.Right = &TreeNode{Val: 20}
    root2.Right.Left = &TreeNode{Val: 15}
    root2.Right.Right = &TreeNode{Val: 7}

    fmt.Println("Exemple 2 - somme max:", maxPathSum(root2))
    // Expected: 42

    // Exemple 3: Nœud unique négatif
    root3 := &TreeNode{Val: -3}
    fmt.Println("Exemple 3 - nœud unique:", maxPathSum(root3))
    // Expected: -3

    // Exemple 4: Chemin avec valeurs négatives
    //   2
    //  /
    // -1
    root4 := &TreeNode{Val: 2}
    root4.Left = &TreeNode{Val: -1}
    fmt.Println("Exemple 4 - ignorer négatif:", maxPathSum(root4))
    // Expected: 2

    // Test complexe avec plusieurs chemins possibles
    //       1
    //      / \
    //     2   3
    //    /   / \
    //   4   5   6
    //  /     \
    // 7       8
    root5 := &TreeNode{Val: 1}
    root5.Left = &TreeNode{Val: 2}
    root5.Right = &TreeNode{Val: 3}
    root5.Left.Left = &TreeNode{Val: 4}
    root5.Right.Left = &TreeNode{Val: 5}
    root5.Right.Right = &TreeNode{Val: 6}
    root5.Left.Left.Left = &TreeNode{Val: 7}
    root5.Right.Left.Right = &TreeNode{Val: 8}

    fmt.Println("Test complexe:", maxPathSum(root5))
    // Expected: 18 (chemin: 7→4→2→1→3→6)
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func maxPathSum(root *TreeNode) int {
    maxSum := math.MinInt32

    var maxGain func(*TreeNode) int
    maxGain = func(node *TreeNode) int {
        if node == nil {
            return 0
        }

        // Calculer le gain maximum des sous-arbres
        // (0 si négatif, on les ignore)
        leftGain := max(maxGain(node.Left), 0)
        rightGain := max(maxGain(node.Right), 0)

        // Somme maximale du chemin passant par ce nœud
        currentMaxPath := node.Val + leftGain + rightGain

        // Mettre à jour le maximum global
        if currentMaxPath > maxSum {
            maxSum = currentMaxPath
        }

        // Retourner le gain maximal que ce nœud peut contribuer à son parent
        // (un seul côté + valeur du nœud)
        return node.Val + max(leftGain, rightGain)
    }

    maxGain(root)
    return maxSum
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```

**Version avec une structure pour clarifier les rôles :**

```go
type PathResult struct {
    maxEndingHere int // Contribution maximale à un parent
    maxOverall    int // Somme maximale globale vue jusqu'ici
}

func maxPathSum(root *TreeNode) int {
    result := dfs(root)
    return result.maxOverall
}

func dfs(node *TreeNode) PathResult {
    if node == nil {
        return PathResult{
            maxEndingHere: 0,
            maxOverall:    math.MinInt32,
        }
    }

    left := dfs(node.Left)
    right := dfs(node.Right)

    // Contribution maximale à un parent (un seul côté)
    maxEndingHere := node.Val + max(0, max(left.maxEndingHere, right.maxEndingHere))

    // Somme maximale passant par ce nœud (les deux côtés)
    maxThroughNode := node.Val + max(0, left.maxEndingHere) + max(0, right.maxEndingHere)

    // Maximum global
    maxOverall := max(maxThroughNode, max(left.maxOverall, right.maxOverall))

    return PathResult{
        maxEndingHere: maxEndingHere,
        maxOverall:    maxOverall,
    }
}
```

**Version avec gestion explicite des cas négatifs :**

```go
func maxPathSum(root *TreeNode) int {
    maxSum := math.MinInt32

    var helper func(*TreeNode) int
    helper = func(node *TreeNode) int {
        if node == nil {
            return 0
        }

        // Récurser sur les enfants
        leftSum := helper(node.Left)
        rightSum := helper(node.Right)

        // Quatre chemins possibles :
        // 1. Seulement le nœud actuel
        onlyNode := node.Val

        // 2. Nœud + sous-arbre gauche
        nodeAndLeft := node.Val + leftSum

        // 3. Nœud + sous-arbre droit
        nodeAndRight := node.Val + rightSum

        // 4. Nœud + les deux sous-arbres (chemin passant par ce nœud)
        nodeAndBoth := node.Val + leftSum + rightSum

        // Mettre à jour le maximum global
        currentMax := max(max(onlyNode, nodeAndLeft), max(nodeAndRight, nodeAndBoth))
        if currentMax > maxSum {
            maxSum = currentMax
        }

        // Retourner la meilleure contribution pour le parent
        // (ne peut pas inclure les deux sous-arbres)
        return max(onlyNode, max(nodeAndLeft, nodeAndRight))
    }

    helper(root)
    return maxSum
}
```

**Simulation pour l'exemple 2 :**

```
      -10
      /  \
     9   20
        /  \
       15   7

maxGain(-10):
  maxGain(9):
    leftGain = rightGain = 0
    currentMaxPath = 9 + 0 + 0 = 9
    maxSum = max(-∞, 9) = 9 ✓
    return 9 + max(0, 0) = 9

  maxGain(20):
    maxGain(15):
      currentMaxPath = 15
      maxSum = max(9, 15) = 15 ✓
      return 15

    maxGain(7):
      currentMaxPath = 7
      maxSum = max(15, 7) = 15
      return 7

    leftGain = max(15, 0) = 15
    rightGain = max(7, 0) = 7
    currentMaxPath = 20 + 15 + 7 = 42
    maxSum = max(15, 42) = 42 ✓
    return 20 + max(15, 7) = 35

  leftGain = max(9, 0) = 9
  rightGain = max(35, 0) = 35
  currentMaxPath = -10 + 9 + 35 = 34
  maxSum = max(42, 34) = 42
  return -10 + max(9, 35) = 25

Résultat: 42 (chemin 15 → 20 → 7)
```

**Complexité :**
- Temps : O(N) où N = nombre de nœuds (chaque nœud visité une fois)
- Espace : O(H) où H = hauteur de l'arbre (pile de récursion)

**Points critiques :**
1. **Deux perspectives** : Contribution au parent vs chemin passant par le nœud
2. **Gestion des négatifs** : Ignorer les contributions négatives avec `max(gain, 0)`
3. **Maximum global** : Mise à jour continue pendant la traversée
4. **Retour de fonction** : Toujours retourner la meilleure contribution (un seul côté)

</details>

## 🎯 Points clés à retenir

1. **Double objectif** : Calculer la contribution ET maintenir le maximum global
2. **Gestion des négatifs** : Ignorer les sous-arbres avec contribution négative
3. **Contrainte de chemin** : Un nœud ne peut pas connecter ses deux enfants à son parent
4. **Optimisation locale** : Chaque nœud fait le meilleur choix pour lui-même

## 🚀 Exercices similaires

- [Maximum Subarray](../medium/max-subarray.md) - Version linéaire du problème
- [Diameter of Binary Tree](../medium/diameter.md) - Structure similaire sans valeurs
- [Path Sum III](../medium/path-sum-iii.md) - Chemins avec somme cible

## 🔍 Variations

**Chemin avec nombre de nœuds limité :**
```go
func maxPathSumWithLimit(root *TreeNode, maxNodes int) int {
    maxSum := math.MinInt32

    var dfs func(*TreeNode, int) (int, int) // (somme, nombre de nœuds)
    dfs = func(node *TreeNode, remainingNodes int) (int, int) {
        if node == nil || remainingNodes <= 0 {
            return 0, 0
        }

        leftSum, leftNodes := dfs(node.Left, remainingNodes-1)
        rightSum, rightNodes := dfs(node.Right, remainingNodes-1)

        // Différents chemins possibles selon le nombre de nœuds disponibles
        if leftNodes+rightNodes+1 <= maxNodes {
            currentSum := node.Val + leftSum + rightSum
            if currentSum > maxSum {
                maxSum = currentSum
            }
        }

        // Retourner le meilleur chemin d'un côté
        if leftSum+leftNodes > rightSum+rightNodes && leftNodes+1 <= maxNodes {
            return node.Val + leftSum, leftNodes + 1
        } else if rightNodes+1 <= maxNodes {
            return node.Val + rightSum, rightNodes + 1
        } else {
            return node.Val, 1
        }
    }

    dfs(root, maxNodes)
    return maxSum
}
```

**Compter les chemins avec somme maximale :**
```go
func countMaxPathSum(root *TreeNode) (int, int) { // (somme max, nombre de chemins)
    maxSum := math.MinInt32
    count := 0

    var dfs func(*TreeNode) int
    dfs = func(node *TreeNode) int {
        if node == nil {
            return 0
        }

        left := max(0, dfs(node.Left))
        right := max(0, dfs(node.Right))

        currentSum := node.Val + left + right

        if currentSum > maxSum {
            maxSum = currentSum
            count = 1
        } else if currentSum == maxSum {
            count++
        }

        return node.Val + max(left, right)
    }

    dfs(root)
    return maxSum, count
}
```