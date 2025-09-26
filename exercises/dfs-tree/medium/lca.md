# Exercice - Lowest Common Ancestor of a Binary Tree

**Niveau :** Medium
**Pattern :** DFS on Trees (avec recherche de nœuds)
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

Étant donné un arbre binaire, trouvez le **plus petit ancêtre commun (LCA)** de deux nœuds donnés dans l'arbre.

Selon la définition de LCA sur Wikipedia : "Le plus petit ancêtre commun est défini entre deux nœuds `p` et `q` comme le nœud le plus bas dans l'arbre qui a `p` et `q` comme descendants (où nous permettons à **un nœud d'être un descendant de lui-même**)."

## 🔍 Exemples

**Exemple 1 :**
```
        3
       / \
      5   1
     / \ / \
    6  2 0  8
      / \
     7   4

Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
Output: 3
Explication: Le LCA des nœuds 5 et 1 est 3.
```

**Exemple 2 :**
```
        3
       / \
      5   1
     / \ / \
    6  2 0  8
      / \
     7   4

Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
Output: 5
Explication: Le LCA des nœuds 5 et 4 est 5, puisqu'un nœud peut être un descendant de lui-même.
```

**Exemple 3 :**
```
  1
 /
2

Input: root = [1,2], p = 1, q = 2
Output: 1
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[2, 10^5]`
- `-10^9 <= Node.val <= 10^9`
- Toutes les valeurs `Node.val` sont **uniques**
- `p != q`
- `p` et `q` existent dans l'arbre

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez la **DFS récursive**. Si un nœud contient `p` ou `q`, ou si ses deux sous-arbres contiennent chacun l'un des nœuds cibles, alors c'est le LCA.

</details>

<details>
<summary>Indice 2</summary>

La fonction récursive peut retourner :
- `nil` si ni `p` ni `q` ne sont dans ce sous-arbre
- Le nœud lui-même s'il est `p` ou `q`
- Le LCA si les deux nœuds sont trouvés dans des sous-arbres différents

</details>

<details>
<summary>Indice 3</summary>

Le LCA est trouvé quand :
- Le nœud actuel est `p` ou `q` ET l'autre est dans un sous-arbre
- Les deux nœuds sont dans des sous-arbres différents du nœud actuel

</details>

<details>
<summary>Indice 4</summary>

Pensez bottom-up : les informations remontent des feuilles vers la racine.

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

func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
    // Votre code ici

}

func main() {
    // Construire l'arbre de l'exemple
    //         3
    //        / \
    //       5   1
    //      / \ / \
    //     6  2 0  8
    //       / \
    //      7   4

    root := &TreeNode{Val: 3}
    root.Left = &TreeNode{Val: 5}
    root.Right = &TreeNode{Val: 1}
    root.Left.Left = &TreeNode{Val: 6}
    root.Left.Right = &TreeNode{Val: 2}
    root.Right.Left = &TreeNode{Val: 0}
    root.Right.Right = &TreeNode{Val: 8}
    root.Left.Right.Left = &TreeNode{Val: 7}
    root.Left.Right.Right = &TreeNode{Val: 4}

    // Références vers les nœuds pour les tests
    node5 := root.Left
    node1 := root.Right
    node4 := root.Left.Right.Right

    // Exemple 1: LCA de 5 et 1
    lca1 := lowestCommonAncestor(root, node5, node1)
    fmt.Println("LCA de 5 et 1:", lca1.Val)
    // Expected: 3

    // Exemple 2: LCA de 5 et 4
    lca2 := lowestCommonAncestor(root, node5, node4)
    fmt.Println("LCA de 5 et 4:", lca2.Val)
    // Expected: 5

    // Test avec arbre simple
    simple := &TreeNode{Val: 1}
    simple.Left = &TreeNode{Val: 2}

    lca3 := lowestCommonAncestor(simple, simple, simple.Left)
    fmt.Println("LCA de 1 et 2:", lca3.Val)
    // Expected: 1
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }

    // Si le nœud actuel est p ou q, c'est un candidat LCA
    if root == p || root == q {
        return root
    }

    // Chercher dans les sous-arbres
    left := lowestCommonAncestor(root.Left, p, q)
    right := lowestCommonAncestor(root.Right, p, q)

    // Si les deux sous-arbres contiennent un des nœuds,
    // le nœud actuel est le LCA
    if left != nil && right != nil {
        return root
    }

    // Sinon, retourner le sous-arbre qui contient un nœud
    if left != nil {
        return left
    }
    return right
}
```

**Version avec valeurs plutôt que références :**

```go
func lowestCommonAncestorByValue(root *TreeNode, pVal, qVal int) *TreeNode {
    if root == nil {
        return nil
    }

    if root.Val == pVal || root.Val == qVal {
        return root
    }

    left := lowestCommonAncestorByValue(root.Left, pVal, qVal)
    right := lowestCommonAncestorByValue(root.Right, pVal, qVal)

    if left != nil && right != nil {
        return root
    }

    if left != nil {
        return left
    }
    return right
}
```

**Version itérative avec chemins :**

```go
func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
    // Trouver le chemin vers p
    pathToP := findPath(root, p)
    if len(pathToP) == 0 {
        return nil
    }

    // Trouver le chemin vers q
    pathToQ := findPath(root, q)
    if len(pathToQ) == 0 {
        return nil
    }

    // Trouver le dernier ancêtre commun
    var lca *TreeNode
    minLen := len(pathToP)
    if len(pathToQ) < minLen {
        minLen = len(pathToQ)
    }

    for i := 0; i < minLen; i++ {
        if pathToP[i] == pathToQ[i] {
            lca = pathToP[i]
        } else {
            break
        }
    }

    return lca
}

func findPath(root, target *TreeNode) []*TreeNode {
    if root == nil {
        return nil
    }

    if root == target {
        return []*TreeNode{root}
    }

    // Chercher dans le sous-arbre gauche
    leftPath := findPath(root.Left, target)
    if leftPath != nil {
        return append([]*TreeNode{root}, leftPath...)
    }

    // Chercher dans le sous-arbre droit
    rightPath := findPath(root.Right, target)
    if rightPath != nil {
        return append([]*TreeNode{root}, rightPath...)
    }

    return nil
}
```

**Simulation pour l'exemple 1 (p=5, q=1) :**

```
        3
       / \
      5   1

lowestCommonAncestor(3, 5, 1):
  3 ≠ 5 et 3 ≠ 1

  left = lowestCommonAncestor(5, 5, 1):
    5 = 5 → return 5 ✓

  right = lowestCommonAncestor(1, 5, 1):
    1 = 1 → return 1 ✓

  left ≠ nil (5) et right ≠ nil (1)
  → return 3 (LCA trouvé) ✓

Résultat: 3
```

**Simulation pour l'exemple 2 (p=5, q=4) :**

```
        3
       / \
      5   1
     / \
    6   2
       / \
      7   4

lowestCommonAncestor(3, 5, 4):
  3 ≠ 5 et 3 ≠ 4

  left = lowestCommonAncestor(5, 5, 4):
    5 = 5 → return 5 ✓

  right = lowestCommonAncestor(1, 5, 4):
    Ni 5 ni 4 ne sont dans le sous-arbre de 1
    → return nil

  left ≠ nil (5) et right = nil
  → return left (5) ✓

Résultat: 5
```

**Complexité :**
- Temps : O(N) où N = nombre de nœuds (dans le pire cas, on visite tous les nœuds)
- Espace : O(H) où H = hauteur de l'arbre (pile de récursion)

**Points critiques :**
1. **Cas de base** : Si le nœud actuel est p ou q, le retourner immédiatement
2. **Logique de combinaison** : Si les deux sous-arbres retournent non-null, c'est le LCA
3. **Propagation** : Remonter le nœud trouvé vers les parents
4. **Garantie d'existence** : Les nœuds p et q existent toujours dans l'arbre

</details>

## 🎯 Points clés à retenir

1. **Approche bottom-up** : Les informations remontent des feuilles vers la racine
2. **Trois cas de retour** : `nil`, un nœud cible, ou le LCA
3. **Condition LCA** : Quand les deux sous-arbres retournent non-null
4. **Auto-ancestralité** : Un nœud peut être l'ancêtre de lui-même

## 🚀 Exercices similaires

- [LCA of Binary Search Tree](../easy/lca-bst.md) - Version optimisée pour BST
- [LCA of Deepest Leaves](../medium/lca-deepest.md) - LCA des feuilles les plus profondes
- [Distance Between Nodes](../medium/node-distance.md) - Distance entre deux nœuds

## 🔍 Variations

**LCA de plusieurs nœuds :**
```go
func lowestCommonAncestorMultiple(root *TreeNode, nodes []*TreeNode) *TreeNode {
    if root == nil || len(nodes) == 0 {
        return nil
    }

    // Vérifier si le nœud actuel est dans la liste
    for _, node := range nodes {
        if root == node {
            return root
        }
    }

    left := lowestCommonAncestorMultiple(root.Left, nodes)
    right := lowestCommonAncestorMultiple(root.Right, nodes)

    if left != nil && right != nil {
        return root
    }

    if left != nil {
        return left
    }
    return right
}
```

**Distance entre deux nœuds via LCA :**
```go
func distanceBetweenNodes(root, p, q *TreeNode) int {
    lca := lowestCommonAncestor(root, p, q)

    distP := distanceFromAncestor(lca, p, 0)
    distQ := distanceFromAncestor(lca, q, 0)

    return distP + distQ
}

func distanceFromAncestor(root, target *TreeNode, dist int) int {
    if root == nil {
        return -1
    }

    if root == target {
        return dist
    }

    left := distanceFromAncestor(root.Left, target, dist+1)
    if left >= 0 {
        return left
    }

    return distanceFromAncestor(root.Right, target, dist+1)
}
```