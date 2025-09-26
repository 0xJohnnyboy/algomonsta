# Exercice - Path Sum

**Niveau :** Easy
**Pattern :** DFS on Trees
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

Étant donné la racine d'un arbre binaire et un entier `targetSum`, retournez `true` s'il existe un **chemin racine-feuille** tel que la somme de toutes les valeurs des nœuds le long du chemin soit égale à `targetSum`.

Un **nœud feuille** est un nœud qui n'a pas d'enfants.

## 🔍 Exemples

**Exemple 1 :**
```
        5
       / \
      4   8
     /   / \
    11  13  4
   / \      \
  7   2      1

Input: root = [5,4,8,11,null,13,4,7,2,null,null,null,1], targetSum = 22
Output: true
Explication: Le chemin racine-feuille avec la somme 22 est 5 → 4 → 11 → 2.
```

**Exemple 2 :**
```
    1
   / \
  2   3

Input: root = [1,2,3], targetSum = 5
Output: false
Explication: Il y a deux chemins racine-feuille dans l'arbre :
(1 → 2) : la somme est 3
(1 → 3) : la somme est 4
Aucun chemin n'a une somme de 5.
```

**Exemple 3 :**
```
Input: root = [], targetSum = 0
Output: false
Explication: Puisque l'arbre est vide, il n'y a pas de chemin racine-feuille.
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[0, 5000]`
- `-1000 <= Node.val <= 1000`
- `-1000 <= targetSum <= 1000`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème classique de **DFS récursive**. Pour chaque nœud, soustrayez sa valeur de `targetSum` et continuez la recherche.

</details>

<details>
<summary>Indice 2</summary>

Cas de base : si vous atteignez une feuille, vérifiez si la valeur du nœud est égale au `targetSum` restant.

</details>

<details>
<summary>Indice 3</summary>

Un nœud feuille est défini par `node.Left == nil && node.Right == nil`.

</details>

<details>
<summary>Indice 4</summary>

Utilisez l'opérateur OR logique : retournez `true` si au moins un des sous-arbres contient un chemin valide.

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

func hasPathSum(root *TreeNode, targetSum int) bool {
    // Votre code ici

}

func main() {
    // Construire l'arbre de l'exemple 1
    //         5
    //        / \
    //       4   8
    //      /   / \
    //     11  13  4
    //    / \      \
    //   7   2      1

    root := &TreeNode{Val: 5}
    root.Left = &TreeNode{Val: 4}
    root.Right = &TreeNode{Val: 8}
    root.Left.Left = &TreeNode{Val: 11}
    root.Right.Left = &TreeNode{Val: 13}
    root.Right.Right = &TreeNode{Val: 4}
    root.Left.Left.Left = &TreeNode{Val: 7}
    root.Left.Left.Right = &TreeNode{Val: 2}
    root.Right.Right.Right = &TreeNode{Val: 1}

    fmt.Println("Chemin avec somme 22 existe:", hasPathSum(root, 22))
    // Expected: true

    fmt.Println("Chemin avec somme 26 existe:", hasPathSum(root, 26))
    // Expected: true (5+8+13)

    fmt.Println("Chemin avec somme 100 existe:", hasPathSum(root, 100))
    // Expected: false

    // Test avec un arbre vide
    fmt.Println("Arbre vide:", hasPathSum(nil, 0))
    // Expected: false

    // Test avec un seul nœud
    single := &TreeNode{Val: 5}
    fmt.Println("Nœud unique (5, target=5):", hasPathSum(single, 5))
    // Expected: true
    fmt.Println("Nœud unique (5, target=1):", hasPathSum(single, 1))
    // Expected: false
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func hasPathSum(root *TreeNode, targetSum int) bool {
    if root == nil {
        return false
    }

    // Si c'est une feuille, vérifier si la somme correspond
    if root.Left == nil && root.Right == nil {
        return root.Val == targetSum
    }

    // Chercher dans les sous-arbres avec la somme mise à jour
    remainingSum := targetSum - root.Val
    return hasPathSum(root.Left, remainingSum) || hasPathSum(root.Right, remainingSum)
}
```

**Version alternative avec somme courante :**

```go
func hasPathSum(root *TreeNode, targetSum int) bool {
    return dfs(root, 0, targetSum)
}

func dfs(node *TreeNode, currentSum, targetSum int) bool {
    if node == nil {
        return false
    }

    currentSum += node.Val

    // Si c'est une feuille, vérifier la somme
    if node.Left == nil && node.Right == nil {
        return currentSum == targetSum
    }

    // Explorer les enfants
    return dfs(node.Left, currentSum, targetSum) || dfs(node.Right, currentSum, targetSum)
}
```

**Version itérative avec stack :**

```go
func hasPathSum(root *TreeNode, targetSum int) bool {
    if root == nil {
        return false
    }

    type pair struct {
        node *TreeNode
        sum  int
    }

    stack := []pair{{root, targetSum - root.Val}}

    for len(stack) > 0 {
        current := stack[len(stack)-1]
        stack = stack[:len(stack)-1]

        node := current.node
        remainingSum := current.sum

        // Si c'est une feuille, vérifier la somme
        if node.Left == nil && node.Right == nil {
            if remainingSum == 0 {
                return true
            }
        }

        // Ajouter les enfants à la stack
        if node.Right != nil {
            stack = append(stack, pair{node.Right, remainingSum - node.Right.Val})
        }
        if node.Left != nil {
            stack = append(stack, pair{node.Left, remainingSum - node.Left.Val})
        }
    }

    return false
}
```

**Simulation pour l'arbre d'exemple avec targetSum = 22 :**

```
hasPathSum(5, 22):
  5 n'est pas une feuille
  remainingSum = 22 - 5 = 17

  hasPathSum(4, 17):  // sous-arbre gauche
    4 n'est pas une feuille
    remainingSum = 17 - 4 = 13

    hasPathSum(11, 13):
      11 n'est pas une feuille
      remainingSum = 13 - 11 = 2

      hasPathSum(7, 2):  // sous-arbre gauche
        7 est une feuille
        7 ≠ 2 → false

      hasPathSum(2, 2):  // sous-arbre droit
        2 est une feuille
        2 = 2 → true ✓

      return true (car au moins un sous-arbre retourne true)
    return true
  return true

Résultat: true
```

**Complexité :**
- Temps : O(N) où N = nombre de nœuds
  - Dans le pire cas, on visite tous les nœuds (pas de chemin valide trouvé)
- Espace : O(H) où H = hauteur de l'arbre (pile de récursion)
  - Meilleur cas (arbre équilibré) : O(log N)
  - Pire cas (arbre dégénéré) : O(N)

**Points critiques :**
1. **Condition de feuille** : `node.Left == nil && node.Right == nil`
2. **Mise à jour de la somme** : Soustraire la valeur courante
3. **Court-circuit OR** : Dès qu'un chemin est trouvé, on peut retourner `true`
4. **Arbre vide** : Retourner `false` même si `targetSum = 0`

</details>

## 🎯 Points clés à retenir

1. **DFS récursive** : Explorer tous les chemins racine-feuille
2. **Condition de feuille** : Vérifier explicitement les deux enfants
3. **Court-circuit** : Utiliser l'opérateur OR pour arrêter dès qu'un chemin est trouvé
4. **Cas de base** : Gérer l'arbre vide et les feuilles

## 🚀 Exercices similaires

- [Path Sum II](../medium/path-sum-ii.md) - Retourner tous les chemins valides
- [Path Sum III](../medium/path-sum-iii.md) - Chemins ne commençant pas forcément à la racine
- [Maximum Path Sum](../hard/max-path-sum.md) - Chemin avec somme maximale

## 🔍 Variations

**Compter tous les chemins valides :**
```go
func countPathSum(root *TreeNode, targetSum int) int {
    if root == nil {
        return 0
    }

    count := 0
    if root.Left == nil && root.Right == nil {
        if root.Val == targetSum {
            count = 1
        }
    } else {
        remainingSum := targetSum - root.Val
        count = countPathSum(root.Left, remainingSum) + countPathSum(root.Right, remainingSum)
    }

    return count
}
```

**Chemin avec produit cible :**
```go
func hasPathProduct(root *TreeNode, targetProduct int) bool {
    if root == nil {
        return false
    }

    if root.Left == nil && root.Right == nil {
        return root.Val == targetProduct
    }

    if targetProduct%root.Val != 0 {
        return false // Division impossible
    }

    remaining := targetProduct / root.Val
    return hasPathProduct(root.Left, remaining) || hasPathProduct(root.Right, remaining)
}
```