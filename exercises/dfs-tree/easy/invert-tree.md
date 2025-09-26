# Exercice - Invert Binary Tree

**Niveau :** Easy
**Pattern :** DFS on Trees
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

Étant donné la racine d'un arbre binaire, **inversez l'arbre** et retournez sa racine.

Inverser un arbre binaire signifie échanger récursivement les enfants gauche et droit de chaque nœud.

## 🔍 Exemples

**Exemple 1 :**
```
Input:
     4
   /   \
  2     7
 / \   / \
1   3 6   9

Output:
     4
   /   \
  7     2
 / \   / \
9   6 3   1

Explication: L'arbre original est inversé - tous les sous-arbres gauche et droit sont échangés.
```

**Exemple 2 :**
```
Input:
  2
 / \
1   3

Output:
  2
 / \
3   1
```

**Exemple 3 :**
```
Input: root = []
Output: []
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[0, 100]`
- `-100 <= Node.val <= 100`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème de **DFS récursive**. Pour chaque nœud, échangez ses enfants gauche et droit, puis inversez récursivement les sous-arbres.

</details>

<details>
<summary>Indice 2</summary>

Cas de base : si le nœud est `nil`, retournez `nil`.

</details>

<details>
<summary>Indice 3</summary>

L'algorithme : `swap(node.Left, node.Right)`, puis `invertTree(node.Left)` et `invertTree(node.Right)`.

</details>

<details>
<summary>Indice 4</summary>

Vous pouvez aussi résoudre ce problème de manière itérative avec une stack ou une queue.

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

func invertTree(root *TreeNode) *TreeNode {
    // Votre code ici

}

// Fonction helper pour afficher l'arbre (inorder traversal)
func inorderPrint(root *TreeNode) []int {
    if root == nil {
        return []int{}
    }
    result := []int{}
    result = append(result, inorderPrint(root.Left)...)
    result = append(result, root.Val)
    result = append(result, inorderPrint(root.Right)...)
    return result
}

func main() {
    // Construire l'arbre de l'exemple 1
    //      4
    //    /   \
    //   2     7
    //  / \   / \
    // 1   3 6   9

    root := &TreeNode{Val: 4}
    root.Left = &TreeNode{Val: 2}
    root.Right = &TreeNode{Val: 7}
    root.Left.Left = &TreeNode{Val: 1}
    root.Left.Right = &TreeNode{Val: 3}
    root.Right.Left = &TreeNode{Val: 6}
    root.Right.Right = &TreeNode{Val: 9}

    fmt.Println("Avant inversion (inorder):", inorderPrint(root))
    // Expected: [1 2 3 4 6 7 9]

    inverted := invertTree(root)
    fmt.Println("Après inversion (inorder):", inorderPrint(inverted))
    // Expected: [9 7 6 4 3 2 1]

    // Test avec un arbre vide
    fmt.Println("Arbre vide:", invertTree(nil))
    // Expected: <nil>

    // Test avec un seul nœud
    single := &TreeNode{Val: 1}
    invertedSingle := invertTree(single)
    fmt.Println("Nœud unique:", inorderPrint(invertedSingle))
    // Expected: [1]
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func invertTree(root *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }

    // Échanger les enfants gauche et droit
    root.Left, root.Right = root.Right, root.Left

    // Inverser récursivement les sous-arbres
    invertTree(root.Left)
    invertTree(root.Right)

    return root
}
```

**Version alternative (inversion puis récursion) :**

```go
func invertTree(root *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }

    // Inverser récursivement les sous-arbres d'abord
    left := invertTree(root.Left)
    right := invertTree(root.Right)

    // Puis échanger
    root.Left = right
    root.Right = left

    return root
}
```

**Version itérative avec queue (BFS) :**

```go
func invertTree(root *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }

    queue := []*TreeNode{root}

    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:]

        // Échanger les enfants
        node.Left, node.Right = node.Right, node.Left

        // Ajouter les enfants à la queue
        if node.Left != nil {
            queue = append(queue, node.Left)
        }
        if node.Right != nil {
            queue = append(queue, node.Right)
        }
    }

    return root
}
```

**Version itérative avec stack (DFS) :**

```go
func invertTree(root *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }

    stack := []*TreeNode{root}

    for len(stack) > 0 {
        node := stack[len(stack)-1]
        stack = stack[:len(stack)-1]

        // Échanger les enfants
        node.Left, node.Right = node.Right, node.Left

        // Ajouter les enfants à la stack
        if node.Left != nil {
            stack = append(stack, node.Left)
        }
        if node.Right != nil {
            stack = append(stack, node.Right)
        }
    }

    return root
}
```

**Simulation pour l'arbre d'exemple :**

```
Original:     4           Étape 1: Inverser 4
           /     \                    4
          2       7                 /   \
         / \     / \               7     2
        1   3   6   9             / \   / \
                                 6   9 1   3

Étape 2: Inverser 7    Étape 3: Inverser 2
        4                      4
      /   \                  /   \
     7     2                7     2
    / \   / \              / \   / \
   9   6 1   3            9   6 3   1

Final:      4
          /   \
         7     2
        / \   / \
       9   6 3   1
```

**Complexité :**
- Temps : O(N) où N = nombre de nœuds (on visite chaque nœud une fois)
- Espace :
  - Récursif : O(H) où H = hauteur de l'arbre (pile de récursion)
  - Itératif : O(W) où W = largeur maximale de l'arbre (queue/stack)

**Points critiques :**
1. **Modification en place** : L'arbre original est modifié
2. **Échange simple** : `root.Left, root.Right = root.Right, root.Left`
3. **Récursion sur les deux sous-arbres** : Ne pas oublier d'inverser les enfants
4. **Retour de la racine** : Importante pour chaîner les opérations

</details>

## 🎯 Points clés à retenir

1. **DFS simple** : Problème récursif naturel sur les arbres
2. **Échange en place** : Utiliser l'assignation multiple de Go
3. **Modification de structure** : L'arbre original est transformé
4. **Base case** : Gérer le cas `nil`

## 🚀 Exercices similaires

- [Symmetric Tree](../easy/symmetric-tree.md) - Vérifier si un arbre est symétrique
- [Same Tree](../easy/same-tree.md) - Comparer deux arbres
- [Mirror Tree](../easy/mirror-tree.md) - Créer un miroir sans modifier l'original

## 🔍 Variations

**Vérifier si un arbre est son propre inverse :**
```go
func isSymmetric(root *TreeNode) bool {
    if root == nil {
        return true
    }
    return isMirror(root.Left, root.Right)
}

func isMirror(left, right *TreeNode) bool {
    if left == nil && right == nil {
        return true
    }
    if left == nil || right == nil {
        return false
    }
    return left.Val == right.Val &&
           isMirror(left.Left, right.Right) &&
           isMirror(left.Right, right.Left)
}
```

**Créer une copie inversée :**
```go
func copyInvertTree(root *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }

    newNode := &TreeNode{Val: root.Val}
    newNode.Left = copyInvertTree(root.Right)
    newNode.Right = copyInvertTree(root.Left)

    return newNode
}
```