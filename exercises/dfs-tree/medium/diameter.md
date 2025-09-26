# Exercice - Diameter of Binary Tree

**Niveau :** Medium
**Pattern :** DFS on Trees (avec calcul global)
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

Étant donné la racine d'un arbre binaire, retournez la **longueur du diamètre** de l'arbre.

Le **diamètre** d'un arbre binaire est la **longueur du plus long chemin** entre deux nœuds quelconques dans l'arbre. Ce chemin peut ou ne peut pas passer par la racine.

La **longueur d'un chemin** entre deux nœuds est représentée par le **nombre d'arêtes** entre eux.

## 🔍 Exemples

**Exemple 1 :**
```
      1
     / \
    2   3
   / \
  4   5

Input: root = [1,2,3,4,5]
Output: 3
Explication: Le plus long chemin est [4,2,1,3] ou [5,2,1,3] avec longueur 3.
```

**Exemple 2 :**
```
  1
 /
2

Input: root = [1,2]
Output: 1
Explication: Le plus long chemin est [1,2] avec longueur 1.
```

**Exemple 3 :**
```
    1
   / \
  2   3
 / \   \
4   5   6
       /
      7

Input: root = [1,2,3,4,5,null,6,null,null,null,null,null,7]
Output: 4
Explication: Le plus long chemin est [4,2,1,3,6,7] avec longueur 5 arêtes.
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[1, 10^4]`
- `-100 <= Node.val <= 100`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Le diamètre passant par un nœud donné est la somme des **hauteurs de ses sous-arbres gauche et droit**.

</details>

<details>
<summary>Indice 2</summary>

Utilisez une approche **DFS** où chaque nœud calcule sa hauteur et met à jour le diamètre global.

</details>

<details>
<summary>Indice 3</summary>

Pour chaque nœud, le diamètre maximal est `max(diametre_actuel, hauteur_gauche + hauteur_droite)`.

</details>

<details>
<summary>Indice 4</summary>

La hauteur d'un nœud est `1 + max(hauteur_gauche, hauteur_droite)`.

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

func diameterOfBinaryTree(root *TreeNode) int {
    // Votre code ici

}

func main() {
    // Construire l'arbre de l'exemple 1
    //       1
    //      / \
    //     2   3
    //    / \
    //   4   5

    root := &TreeNode{Val: 1}
    root.Left = &TreeNode{Val: 2}
    root.Right = &TreeNode{Val: 3}
    root.Left.Left = &TreeNode{Val: 4}
    root.Left.Right = &TreeNode{Val: 5}

    fmt.Println("Exemple 1 - diamètre:", diameterOfBinaryTree(root))
    // Expected: 3

    // Test avec un chemin linéaire
    //   1
    //  /
    // 2
    linear := &TreeNode{Val: 1}
    linear.Left = &TreeNode{Val: 2}

    fmt.Println("Chemin linéaire:", diameterOfBinaryTree(linear))
    // Expected: 1

    // Test avec un seul nœud
    single := &TreeNode{Val: 1}
    fmt.Println("Nœud unique:", diameterOfBinaryTree(single))
    // Expected: 0

    // Construire l'arbre de l'exemple 3
    //     1
    //    / \
    //   2   3
    //  / \   \
    // 4   5   6
    //        /
    //       7

    root2 := &TreeNode{Val: 1}
    root2.Left = &TreeNode{Val: 2}
    root2.Right = &TreeNode{Val: 3}
    root2.Left.Left = &TreeNode{Val: 4}
    root2.Left.Right = &TreeNode{Val: 5}
    root2.Right.Right = &TreeNode{Val: 6}
    root2.Right.Right.Left = &TreeNode{Val: 7}

    fmt.Println("Exemple 3 - diamètre:", diameterOfBinaryTree(root2))
    // Expected: 4
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func diameterOfBinaryTree(root *TreeNode) int {
    maxDiameter := 0

    var height func(*TreeNode) int
    height = func(node *TreeNode) int {
        if node == nil {
            return 0
        }

        // Calculer les hauteurs des sous-arbres
        leftHeight := height(node.Left)
        rightHeight := height(node.Right)

        // Mettre à jour le diamètre maximal
        currentDiameter := leftHeight + rightHeight
        if currentDiameter > maxDiameter {
            maxDiameter = currentDiameter
        }

        // Retourner la hauteur de ce nœud
        return 1 + max(leftHeight, rightHeight)
    }

    height(root)
    return maxDiameter
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```

**Version avec une structure pour retourner les deux valeurs :**

```go
type Result struct {
    height   int
    diameter int
}

func diameterOfBinaryTree(root *TreeNode) int {
    result := dfs(root)
    return result.diameter
}

func dfs(node *TreeNode) Result {
    if node == nil {
        return Result{height: 0, diameter: 0}
    }

    left := dfs(node.Left)
    right := dfs(node.Right)

    // Hauteur de ce nœud
    height := 1 + max(left.height, right.height)

    // Diamètre maximal : soit passant par ce nœud, soit dans un sous-arbre
    diameter := max(left.height+right.height, max(left.diameter, right.diameter))

    return Result{height: height, diameter: diameter}
}
```

**Version avec pointeur pour éviter la variable globale :**

```go
func diameterOfBinaryTree(root *TreeNode) int {
    diameter := 0
    calculateHeight(root, &diameter)
    return diameter
}

func calculateHeight(node *TreeNode, diameter *int) int {
    if node == nil {
        return 0
    }

    leftHeight := calculateHeight(node.Left, diameter)
    rightHeight := calculateHeight(node.Right, diameter)

    // Mettre à jour le diamètre si nécessaire
    currentDiameter := leftHeight + rightHeight
    if currentDiameter > *diameter {
        *diameter = currentDiameter
    }

    return 1 + max(leftHeight, rightHeight)
}
```

**Simulation pour l'arbre d'exemple 1 :**

```
        1
       / \
      2   3
     / \
    4   5

height(1):
  height(2):  // sous-arbre gauche
    height(4): return 1 (hauteur = 0+0+1 = 1, diamètre = 0)
    height(5): return 1 (hauteur = 0+0+1 = 1, diamètre = 0)

    leftHeight = 1, rightHeight = 1
    currentDiameter = 1 + 1 = 2
    maxDiameter = max(0, 2) = 2 ✓
    return 1 + max(1, 1) = 2

  height(3):  // sous-arbre droit
    leftHeight = 0, rightHeight = 0
    currentDiameter = 0 + 0 = 0
    maxDiameter = max(2, 0) = 2
    return 1 + max(0, 0) = 1

  leftHeight = 2, rightHeight = 1
  currentDiameter = 2 + 1 = 3
  maxDiameter = max(2, 3) = 3 ✓
  return 1 + max(2, 1) = 3

Résultat: 3
```

**Visualisation du plus long chemin :**

```
        1
       / \
      2   3
     / \
    4   5

Plus long chemin : 4 → 2 → 1 → 3
Nombre d'arêtes : 3
```

**Complexité :**
- Temps : O(N) où N = nombre de nœuds (on visite chaque nœud une fois)
- Espace : O(H) où H = hauteur de l'arbre (pile de récursion)
  - Meilleur cas (arbre équilibré) : O(log N)
  - Pire cas (arbre dégénéré) : O(N)

**Points critiques :**
1. **Diamètre vs Hauteur** : Le diamètre est calculé, la hauteur est retournée
2. **Variable globale** : Utiliser une variable partagée ou un pointeur
3. **Cas de base** : Nœud `nil` a une hauteur de 0
4. **Mise à jour continue** : Vérifier le diamètre à chaque nœud

</details>

## 🎯 Points clés à retenir

1. **Double objectif** : Calculer la hauteur ET maintenir le diamètre maximal
2. **Formule clé** : Diamètre = hauteur_gauche + hauteur_droite
3. **Optimisation** : Une seule traversée DFS suffit
4. **Variable globale** : Nécessaire pour maintenir le maximum global

## 🚀 Exercices similaires

- [Maximum Depth of Binary Tree](../easy/max-depth.md) - Hauteur d'un arbre
- [Balanced Binary Tree](../easy/balanced-tree.md) - Vérifier l'équilibre
- [Binary Tree Maximum Path Sum](../hard/max-path-sum.md) - Somme maximale d'un chemin

## 🔍 Variations

**Diamètre avec valeurs des nœuds :**
```go
func diameterWithValues(root *TreeNode) int {
    maxSum := math.MinInt32

    var maxPath func(*TreeNode) int
    maxPath = func(node *TreeNode) int {
        if node == nil {
            return 0
        }

        left := maxPath(node.Left)
        right := maxPath(node.Right)

        // Somme maximale passant par ce nœud
        currentSum := node.Val + left + right
        if currentSum > maxSum {
            maxSum = currentSum
        }

        // Retourner la meilleure somme d'un côté
        return node.Val + max(left, right)
    }

    maxPath(root)
    return maxSum
}
```

**Compter les chemins de longueur donnée :**
```go
func countPathsOfLength(root *TreeNode, targetLength int) int {
    count := 0

    var dfs func(*TreeNode) int
    dfs = func(node *TreeNode) int {
        if node == nil {
            return 0
        }

        left := dfs(node.Left)
        right := dfs(node.Right)

        // Vérifier si un chemin de la longueur cible passe par ce nœud
        if left+right == targetLength {
            count++
        }

        return 1 + max(left, right)
    }

    dfs(root)
    return count
}
```