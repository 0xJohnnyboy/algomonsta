# Exercice - Path Sum II

**Niveau :** Medium  
**Pattern :** DFS on Trees (avec backtracking)  
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

Étant donné la racine d'un arbre binaire et un entier `targetSum`, retournez **tous les chemins racine-feuille** où la somme des valeurs des nœuds du chemin est égale à `targetSum`.

Chaque chemin doit être retourné sous forme de liste des valeurs des nœuds, **pas des références de nœuds**.

Un **nœud feuille** est un nœud qui n'a pas d'enfants.

## 🔍 Exemples

**Exemple 1 :**
```
        5
       / \
      4   8
     /   / \
    11  13  4
   / \    / \
  7   2  5   1

Input: root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
Output: [[5,4,11,2],[5,8,4,5]]
Explication: Il y a deux chemins dont la somme est 22:
5 + 4 + 11 + 2 = 22
5 + 8 + 4 + 5 = 22
```

**Exemple 2 :**
```
     1
    / \
   2   3

Input: root = [1,2,3], targetSum = 5  
Output: []
Explication: Il n'y a pas de chemin racine-feuille avec une somme de 5.
```

**Exemple 3 :**
```
  1
 /
2

Input: root = [1,2], targetSum = 0
Output: []
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[0, 5000]`
- `-1000 <= Node.val <= 1000`
- `-1000 <= targetSum <= 1000`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème de DFS avec **backtracking**. Vous devez explorer tous les chemins racine-feuille et collecter ceux qui satisfont la condition.

</details>

<details>
<summary>Indice 2</summary>

Maintenez un chemin actuel (`currentPath`) et une somme actuelle (`currentSum`). Quand vous atteignez une feuille, vérifiez si la somme correspond.

</details>

<details>
<summary>Indice 3</summary>

**Backtracking important :** Après avoir exploré un sous-arbre, retirez le nœud du chemin actuel pour ne pas affecter les autres explorations.

</details>

<details>
<summary>Indice 4</summary>

Utilisez une fonction helper récursive qui prend le nœud actuel, le chemin actuel, et la somme restante à atteindre.

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

func pathSum(root *TreeNode, targetSum int) [][]int {
    // Votre code ici
    
}

func main() {
    // Construire l'arbre de l'exemple 1
    //        5
    //       / \
    //      4   8
    //     /   / \
    //    11  13  4
    //   / \    / \
    //  7   2  5   1
    
    root := &TreeNode{Val: 5}
    root.Left = &TreeNode{Val: 4}
    root.Right = &TreeNode{Val: 8}
    root.Left.Left = &TreeNode{Val: 11}
    root.Right.Left = &TreeNode{Val: 13}
    root.Right.Right = &TreeNode{Val: 4}
    root.Left.Left.Left = &TreeNode{Val: 7}
    root.Left.Left.Right = &TreeNode{Val: 2}
    root.Right.Right.Left = &TreeNode{Val: 5}
    root.Right.Right.Right = &TreeNode{Val: 1}
    
    result := pathSum(root, 22)
    fmt.Println("Chemins trouvés:", result)
    // Expected: [[5 4 11 2] [5 8 4 5]]
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func pathSum(root *TreeNode, targetSum int) [][]int {
    var result [][]int
    var currentPath []int
    
    var dfs func(*TreeNode, int)
    dfs = func(node *TreeNode, remainingSum int) {
        if node == nil {
            return
        }
        
        // Ajouter le nœud actuel au chemin
        currentPath = append(currentPath, node.Val)
        remainingSum -= node.Val
        
        // Si c'est une feuille et la somme correspond
        if node.Left == nil && node.Right == nil && remainingSum == 0 {
            // Important: faire une copie du chemin actuel
            path := make([]int, len(currentPath))
            copy(path, currentPath)
            result = append(result, path)
        } else {
            // Explorer les enfants
            dfs(node.Left, remainingSum)
            dfs(node.Right, remainingSum)
        }
        
        // Backtracking: retirer le nœud du chemin
        currentPath = currentPath[:len(currentPath)-1]
    }
    
    dfs(root, targetSum)
    return result
}
```

**Version alternative avec passage par valeur :**

```go
func pathSum(root *TreeNode, targetSum int) [][]int {
    var result [][]int
    
    var dfs func(*TreeNode, []int, int)
    dfs = func(node *TreeNode, path []int, remainingSum int) {
        if node == nil {
            return
        }
        
        // Ajouter le nœud actuel (crée une nouvelle slice)
        newPath := append(path, node.Val)
        remainingSum -= node.Val
        
        // Si c'est une feuille et la somme correspond
        if node.Left == nil && node.Right == nil && remainingSum == 0 {
            result = append(result, newPath)
            return
        }
        
        // Explorer les enfants avec le nouveau chemin
        dfs(node.Left, newPath, remainingSum)
        dfs(node.Right, newPath, remainingSum)
        
        // Pas besoin de backtracking explicite car newPath est local
    }
    
    dfs(root, []int{}, targetSum)
    return result
}
```

**Simulation pour l'arbre d'exemple avec targetSum = 22 :**

```
DFS(5, [], 22):
  path = [5], remaining = 17
  DFS(4, [5], 17):
    path = [5,4], remaining = 13
    DFS(11, [5,4], 13):
      path = [5,4,11], remaining = 2
      DFS(7, [5,4,11], 2):
        path = [5,4,11,7], remaining = -5
        7 est une feuille, -5 ≠ 0 → pas de solution
      DFS(2, [5,4,11], 2):
        path = [5,4,11,2], remaining = 0
        2 est une feuille, 0 = 0 → SOLUTION TROUVÉE: [5,4,11,2]
  DFS(8, [5], 17):
    path = [5,8], remaining = 9
    DFS(13, [5,8], 9):
      path = [5,8,13], remaining = -4
      13 est une feuille, -4 ≠ 0 → pas de solution
    DFS(4, [5,8], 9):
      path = [5,8,4], remaining = 5
      DFS(5, [5,8,4], 5):
        path = [5,8,4,5], remaining = 0
        5 est une feuille, 0 = 0 → SOLUTION TROUVÉE: [5,8,4,5]
      DFS(1, [5,8,4], 5):
        path = [5,8,4,1], remaining = 4
        1 est une feuille, 4 ≠ 0 → pas de solution

Résultat: [[5,4,11,2], [5,8,4,5]]
```

**Explication des deux approches :**

1. **Avec backtracking explicite :**
   - Plus efficient en mémoire (une seule slice partagée)
   - Nécessite de faire `copy()` lors de l'ajout au résultat
   - Backtracking manuel avec `currentPath = currentPath[:len(currentPath)-1]`

2. **Avec passage par valeur :**
   - Plus simple à comprendre (pas de backtracking explicite)
   - Utilise plus de mémoire (nouvelles slices à chaque appel)
   - `append(path, node.Val)` crée automatiquement une nouvelle slice

**Complexité :**
- Temps : O(N * H) où N = nombre de nœuds, H = hauteur de l'arbre
  - Dans le pire cas, on copie O(H) éléments pour chaque chemin trouvé
- Espace : O(N * H) pour stocker tous les chemins + O(H) pour la pile de récursion

**Points critiques :**
1. **Copie des chemins** : Essentiel de copier le chemin avant de l'ajouter au résultat
2. **Condition de feuille** : `node.Left == nil && node.Right == nil`
3. **Backtracking** : Retirer le nœud après exploration pour ne pas affecter les autres branches

</details>

## 🎯 Points clés à retenir

1. **DFS avec backtracking** : Explorer tous les chemins puis revenir en arrière
2. **Copie de slice** : Toujours copier avant d'ajouter au résultat
3. **Condition de feuille** : Vérifier explicitement `Left == nil && Right == nil`
4. **Gestion de la somme** : Soustraire au fur et à mesure ou maintenir une somme courante

## 🚀 Exercices similaires

- [Path Sum](../easy/path-sum.md) - Version plus simple (boolean)
- [Path Sum III](../medium/path-sum-iii.md) - Chemins ne commençant pas forcément à la racine
- [Binary Tree Paths](../medium/tree-paths.md) - Tous les chemins (sans condition de somme)

## 🔍 Variations

**Path Sum III (chemins quelconques) :**
```go
func pathSumIII(root *TreeNode, targetSum int) int {
    // Compter tous les chemins (pas seulement racine-feuille)
}
```

**Maximum Path Sum :**
```go
func maxPathSum(root *TreeNode) int {
    // Trouver le chemin avec la somme maximale
}
```
