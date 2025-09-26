# Exercice - Validate Binary Search Tree

**Niveau :** Medium
**Pattern :** DFS on Trees (avec contraintes de bornes)
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

Étant donné la racine d'un arbre binaire, déterminez s'il s'agit d'un **arbre de recherche binaire (BST) valide**.

Un BST valide est défini comme suit :
- La sous-arbre gauche d'un nœud contient uniquement des nœuds avec des valeurs **strictement inférieures** à celle du nœud.
- La sous-arbre droite d'un nœud contient uniquement des nœuds avec des valeurs **strictement supérieures** à celle du nœud.
- Les sous-arbres gauche et droite doivent également être des arbres de recherche binaires.

## 🔍 Exemples

**Exemple 1 :**
```
    2
   / \
  1   3

Input: root = [2,1,3]
Output: true
Explication: C'est un BST valide. 1 < 2 < 3.
```

**Exemple 2 :**
```
    5
   / \
  1   4
     / \
    3   6

Input: root = [5,1,4,null,null,3,6]
Output: false
Explication: La racine a la valeur 5, mais son enfant droit a la valeur 4.
De plus, 3 < 5, donc 3 ne devrait pas être dans le sous-arbre droit de 5.
```

**Exemple 3 :**
```
    10
   /  \
  5   15
     /  \
    6   20

Input: root = [10,5,15,null,null,6,20]
Output: false
Explication: 6 < 10, donc 6 ne devrait pas être dans le sous-arbre droit de 10.
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[1, 10^4]`
- `-2^31 <= Node.val <= 2^31 - 1`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Il ne suffit pas de vérifier que `left.val < node.val < right.val`. Chaque nœud doit respecter des **bornes min et max** définies par ses ancêtres.

</details>

<details>
<summary>Indice 2</summary>

Utilisez la récursion en passant les bornes `min` et `max` autorisées pour chaque nœud.

</details>

<details>
<summary>Indice 3</summary>

Pour le sous-arbre gauche, la borne max devient `node.val`. Pour le sous-arbre droit, la borne min devient `node.val`.

</details>

<details>
<summary>Indice 4</summary>

Alternative : utilisez un parcours in-order et vérifiez que les valeurs sont strictement croissantes.

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

func isValidBST(root *TreeNode) bool {
    // Votre code ici

}

func main() {
    // Exemple 1: BST valide
    //     2
    //    / \
    //   1   3

    root1 := &TreeNode{Val: 2}
    root1.Left = &TreeNode{Val: 1}
    root1.Right = &TreeNode{Val: 3}

    fmt.Println("Exemple 1 - BST valide:", isValidBST(root1))
    // Expected: true

    // Exemple 2: BST invalide
    //     5
    //    / \
    //   1   4
    //      / \
    //     3   6

    root2 := &TreeNode{Val: 5}
    root2.Left = &TreeNode{Val: 1}
    root2.Right = &TreeNode{Val: 4}
    root2.Right.Left = &TreeNode{Val: 3}
    root2.Right.Right = &TreeNode{Val: 6}

    fmt.Println("Exemple 2 - BST invalide:", isValidBST(root2))
    // Expected: false

    // Exemple 3: Cas tricky
    //     10
    //    /  \
    //   5   15
    //      /  \
    //     6   20

    root3 := &TreeNode{Val: 10}
    root3.Left = &TreeNode{Val: 5}
    root3.Right = &TreeNode{Val: 15}
    root3.Right.Left = &TreeNode{Val: 6}
    root3.Right.Right = &TreeNode{Val: 20}

    fmt.Println("Exemple 3 - 6 < 10 (invalide):", isValidBST(root3))
    // Expected: false

    // Test avec un seul nœud
    single := &TreeNode{Val: 1}
    fmt.Println("Nœud unique:", isValidBST(single))
    // Expected: true
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func isValidBST(root *TreeNode) bool {
    return validate(root, math.MinInt64, math.MaxInt64)
}

func validate(node *TreeNode, min, max int64) bool {
    if node == nil {
        return true
    }

    val := int64(node.Val)

    // Vérifier que la valeur est dans les bornes
    if val <= min || val >= max {
        return false
    }

    // Valider les sous-arbres avec les nouvelles bornes
    return validate(node.Left, min, val) && validate(node.Right, val, max)
}
```

**Version avec des pointeurs pour gérer les bornes infinies :**

```go
func isValidBST(root *TreeNode) bool {
    return validatePtr(root, nil, nil)
}

func validatePtr(node *TreeNode, min, max *int) bool {
    if node == nil {
        return true
    }

    // Vérifier la borne inférieure
    if min != nil && node.Val <= *min {
        return false
    }

    // Vérifier la borne supérieure
    if max != nil && node.Val >= *max {
        return false
    }

    // Valider les sous-arbres
    return validatePtr(node.Left, min, &node.Val) && validatePtr(node.Right, &node.Val, max)
}
```

**Version avec parcours in-order :**

```go
func isValidBST(root *TreeNode) bool {
    var prev *int

    var inorder func(*TreeNode) bool
    inorder = func(node *TreeNode) bool {
        if node == nil {
            return true
        }

        // Parcourir le sous-arbre gauche
        if !inorder(node.Left) {
            return false
        }

        // Vérifier la valeur courante
        if prev != nil && node.Val <= *prev {
            return false
        }
        prev = &node.Val

        // Parcourir le sous-arbre droit
        return inorder(node.Right)
    }

    return inorder(root)
}
```

**Version itérative avec stack :**

```go
func isValidBST(root *TreeNode) bool {
    type bounded struct {
        node *TreeNode
        min  int64
        max  int64
    }

    stack := []bounded{{root, math.MinInt64, math.MaxInt64}}

    for len(stack) > 0 {
        current := stack[len(stack)-1]
        stack = stack[:len(stack)-1]

        if current.node == nil {
            continue
        }

        val := int64(current.node.Val)

        if val <= current.min || val >= current.max {
            return false
        }

        // Ajouter les enfants avec leurs bornes
        stack = append(stack, bounded{current.node.Right, val, current.max})
        stack = append(stack, bounded{current.node.Left, current.min, val})
    }

    return true
}
```

**Simulation pour l'exemple 2 (invalide) :**

```
        5
       / \
      1   4
         / \
        3   6

validate(5, -∞, +∞):
  5 dans [-∞, +∞] ✓

  validate(1, -∞, 5):  // sous-arbre gauche
    1 dans [-∞, 5] ✓
    validate(nil, -∞, 1): return true ✓
    validate(nil, 1, 5): return true ✓
    return true ✓

  validate(4, 5, +∞):  // sous-arbre droit
    4 dans [5, +∞] ✗ (4 <= 5)
    return false ✗

return false ✗

Résultat: false
```

**Simulation pour l'exemple 3 (6 < 10) :**

```
validate(10, -∞, +∞):
  validate(15, 10, +∞):
    validate(6, 10, 15):
      6 dans [10, 15] ✗ (6 <= 10)
      return false ✗

Résultat: false
```

**Complexité :**
- Temps : O(N) où N = nombre de nœuds (on visite chaque nœud une fois)
- Espace : O(H) où H = hauteur de l'arbre (pile de récursion)

**Points critiques :**
1. **Bornes strictes** : Utiliser `<` et `>`, pas `<=` et `>=`
2. **Propagation des bornes** : Chaque nœud hérite des contraintes de ses ancêtres
3. **Gestion des entiers** : Attention aux overflows avec `math.MinInt32/MaxInt32`
4. **Parcours in-order** : Doit donner une séquence strictement croissante

</details>

## 🎯 Points clés à retenir

1. **Contraintes de bornes** : Chaque nœud a des bornes min/max héritées
2. **Validation récursive** : Propager les contraintes aux sous-arbres
3. **Comparaisons strictes** : Utiliser `<` et `>`, pas `<=` et `>=`
4. **Alternative in-order** : Parcours in-order donne une séquence croissante

## 🚀 Exercices similaires

- [Lowest Common Ancestor of BST](../easy/lca-bst.md) - LCA dans un BST
- [Search in BST](../easy/search-bst.md) - Recherche dans un BST
- [Insert into BST](../medium/insert-bst.md) - Insertion dans un BST

## 🔍 Variations

**Valider BST avec doublons autorisés :**
```go
func isValidBSTWithDuplicates(root *TreeNode) bool {
    return validateDup(root, math.MinInt64, math.MaxInt64)
}

func validateDup(node *TreeNode, min, max int64) bool {
    if node == nil {
        return true
    }

    val := int64(node.Val)
    if val < min || val > max {
        return false
    }

    // Doublons autorisés : <= pour la gauche, >= pour la droite
    return validateDup(node.Left, min, val) && validateDup(node.Right, val, max)
}
```

**Compter les nœuds violant la propriété BST :**
```go
func countInvalidNodes(root *TreeNode) int {
    count := 0

    var validate func(*TreeNode, int64, int64)
    validate = func(node *TreeNode, min, max int64) {
        if node == nil {
            return
        }

        val := int64(node.Val)
        if val <= min || val >= max {
            count++
        }

        validate(node.Left, min, val)
        validate(node.Right, val, max)
    }

    validate(root, math.MinInt64, math.MaxInt64)
    return count
}
```