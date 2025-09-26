# Exercice - Maximum Depth of Binary Tree

**Niveau :** Easy
**Pattern :** DFS on Trees
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

Étant donné la racine d'un arbre binaire, retournez sa **profondeur maximale**.

La profondeur maximale d'un arbre binaire est le nombre de nœuds le long du chemin le plus long de la racine jusqu'à la feuille la plus éloignée.

## 🔍 Exemples

**Exemple 1 :**
```
    3
   / \
  9  20
    /  \
   15   7

Input: root = [3,9,20,null,null,15,7]
Output: 3
Explication: La profondeur maximale est 3 (3 → 20 → 15 ou 3 → 20 → 7).
```

**Exemple 2 :**
```
  1
   \
    2

Input: root = [1,null,2]
Output: 2
```

**Exemple 3 :**
```
Input: root = []
Output: 0
```

**Exemple 4 :**
```
Input: root = [0]
Output: 1
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[0, 10^4]`
- `-100 <= Node.val <= 100`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème classique de **DFS récursive**. La profondeur d'un arbre est 1 + la profondeur maximale de ses sous-arbres.

</details>

<details>
<summary>Indice 2</summary>

Cas de base : si le nœud est `nil`, la profondeur est 0.

</details>

<details>
<summary>Indice 3</summary>

Utilisez la récursion : `depth(node) = 1 + max(depth(left), depth(right))`

</details>

<details>
<summary>Indice 4</summary>

Vous pouvez aussi résoudre ce problème avec BFS niveau par niveau.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

func maxDepth(root *TreeNode) int {
    // Votre code ici

}

func main() {
    // Construire l'arbre de l'exemple 1
    //     3
    //    / \
    //   9  20
    //     /  \
    //    15   7

    root := &TreeNode{Val: 3}
    root.Left = &TreeNode{Val: 9}
    root.Right = &TreeNode{Val: 20}
    root.Right.Left = &TreeNode{Val: 15}
    root.Right.Right = &TreeNode{Val: 7}

    depth := maxDepth(root)
    fmt.Println("Profondeur maximale:", depth)
    // Expected: 3

    // Test avec un arbre vide
    fmt.Println("Arbre vide:", maxDepth(nil))
    // Expected: 0

    // Test avec un seul nœud
    single := &TreeNode{Val: 1}
    fmt.Println("Nœud unique:", maxDepth(single))
    // Expected: 1
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }

    leftDepth := maxDepth(root.Left)
    rightDepth := maxDepth(root.Right)

    return 1 + max(leftDepth, rightDepth)
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```

**Version plus concise :**

```go
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    return 1 + max(maxDepth(root.Left), maxDepth(root.Right))
}
```

**Version itérative avec BFS :**

```go
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }

    queue := []*TreeNode{root}
    depth := 0

    for len(queue) > 0 {
        size := len(queue)
        depth++

        // Traiter tous les nœuds du niveau actuel
        for i := 0; i < size; i++ {
            node := queue[0]
            queue = queue[1:]

            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
    }

    return depth
}
```

**Simulation pour l'arbre d'exemple :**

```
       3     ← niveau 1 (depth = 1)
      / \
     9  20   ← niveau 2 (depth = 2)
       /  \
      15   7 ← niveau 3 (depth = 3)

maxDepth(3):
  maxDepth(9): return 1 (pas d'enfants)
  maxDepth(20):
    maxDepth(15): return 1
    maxDepth(7): return 1
    return 1 + max(1, 1) = 2
  return 1 + max(1, 2) = 3
```

**Complexité :**
- Temps : O(N) où N = nombre de nœuds (on visite chaque nœud une fois)
- Espace : O(H) où H = hauteur de l'arbre (pile de récursion)
  - Meilleur cas (arbre équilibré) : O(log N)
  - Pire cas (arbre dégénéré) : O(N)

**Points critiques :**
1. **Cas de base** : Gérer correctement `root == nil`
2. **Récursion** : Combiner les résultats des sous-arbres avec `max()`
3. **Définition** : La profondeur inclut le nœud racine (pas 0-indexée)

</details>

## 🎯 Points clés à retenir

1. **DFS récursive classique** : Parfait pour explorer la structure de l'arbre
2. **Cas de base** : Toujours gérer le cas `nil`
3. **Combinaison des résultats** : `1 + max(gauche, droite)`
4. **Complexité** : O(N) en temps, O(H) en espace

## 🚀 Exercices similaires

- [Minimum Depth of Binary Tree](../easy/min-depth.md) - Profondeur minimale
- [Diameter of Binary Tree](../medium/diameter.md) - Plus long chemin entre deux nœuds
- [Balanced Binary Tree](../easy/balanced-tree.md) - Vérifier si l'arbre est équilibré

## 🔍 Variations

**Minimum Depth :**
```go
func minDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    if root.Left == nil && root.Right == nil {
        return 1
    }

    minLeft := math.MaxInt32
    minRight := math.MaxInt32

    if root.Left != nil {
        minLeft = minDepth(root.Left)
    }
    if root.Right != nil {
        minRight = minDepth(root.Right)
    }

    return 1 + min(minLeft, minRight)
}
```

**Compter les nœuds :**
```go
func countNodes(root *TreeNode) int {
    if root == nil {
        return 0
    }
    return 1 + countNodes(root.Left) + countNodes(root.Right)
}
```