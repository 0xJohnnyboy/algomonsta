# Exercice - Binary Tree Level Order Traversal

**Niveau :** Easy
**Pattern :** BFS Niveau par niveau
**Retour au cours :** [BFS on Graphs Pattern](../../courses/06-bfs-graph.md)

## 📝 Énoncé

Étant donné la racine d'un arbre binaire, retournez le **parcours niveau par niveau** de ses valeurs de nœuds (c'est-à-dire, de gauche à droite, niveau par niveau).

## 🔍 Exemples

**Exemple 1 :**
```
Input: root = [3,9,20,null,null,15,7]
        3
       / \
      9  20
         /  \
        15   7
Output: [[3],[9,20],[15,7]]
```

**Exemple 2 :**
```
Input: root = [1]
Output: [[1]]
```

**Exemple 3 :**
```
Input: root = []
Output: []
```

**Exemple 4 :**
```
Input: root = [1,2,3,4,5,6,7]
        1
       / \
      2   3
     / \ / \
    4  5 6  7
Output: [[1],[2,3],[4,5,6,7]]
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[0, 2000]`
- `-1000 <= Node.val <= 1000`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez **BFS avec une queue** pour parcourir l'arbre niveau par niveau. Chaque itération de la boucle principale traite un niveau complet.

</details>

<details>
<summary>Indice 2</summary>

Gardez la taille actuelle de la queue au début de chaque niveau. Cela vous dit combien de nœuds appartiennent au niveau actuel.

</details>

<details>
<summary>Indice 3</summary>

Créez un tableau pour chaque niveau, ajoutez-y les valeurs des nœuds, puis ajoutez ce tableau au résultat final.

</details>

<details>
<summary>Indice 4</summary>

N'oubliez pas d'ajouter les enfants de chaque nœud (s'ils existent) à la queue pour les traiter au niveau suivant.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

// Definition for a binary tree node
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

func levelOrder(root *TreeNode) [][]int {
    // Votre code ici

}

// Helper function to create a tree node
func newNode(val int) *TreeNode {
    return &TreeNode{Val: val}
}

func main() {
    // Test case 1: [3,9,20,null,null,15,7]
    root1 := newNode(3)
    root1.Left = newNode(9)
    root1.Right = newNode(20)
    root1.Right.Left = newNode(15)
    root1.Right.Right = newNode(7)
    fmt.Println("Exemple 1:", levelOrder(root1)) // Expected: [[3],[9,20],[15,7]]

    // Test case 2: [1]
    root2 := newNode(1)
    fmt.Println("Exemple 2:", levelOrder(root2)) // Expected: [[1]]

    // Test case 3: []
    var root3 *TreeNode = nil
    fmt.Println("Exemple 3:", levelOrder(root3)) // Expected: []

    // Test case 4: [1,2,3,4,5,6,7]
    root4 := newNode(1)
    root4.Left = newNode(2)
    root4.Right = newNode(3)
    root4.Left.Left = newNode(4)
    root4.Left.Right = newNode(5)
    root4.Right.Left = newNode(6)
    root4.Right.Right = newNode(7)
    fmt.Println("Exemple 4:", levelOrder(root4)) // Expected: [[1],[2,3],[4,5,6,7]]

    // Test case 5: [1,2]
    root5 := newNode(1)
    root5.Left = newNode(2)
    fmt.Println("Exemple 5:", levelOrder(root5)) // Expected: [[1],[2]]

    // Test case 6: [1,null,2]
    root6 := newNode(1)
    root6.Right = newNode(2)
    fmt.Println("Exemple 6:", levelOrder(root6)) // Expected: [[1],[2]]

    // Test case 7: [5,4,8,11,null,13,4,7,2,null,null,null,1]
    root7 := newNode(5)
    root7.Left = newNode(4)
    root7.Right = newNode(8)
    root7.Left.Left = newNode(11)
    root7.Right.Left = newNode(13)
    root7.Right.Right = newNode(4)
    root7.Left.Left.Left = newNode(7)
    root7.Left.Left.Right = newNode(2)
    root7.Right.Right.Right = newNode(1)
    fmt.Println("Exemple 7:", levelOrder(root7)) // Expected: [[5],[4,8],[11,13,4],[7,2,1]]

    // Test case 8: [0,-3,9,-10,null,5]
    root8 := newNode(0)
    root8.Left = newNode(-3)
    root8.Right = newNode(9)
    root8.Left.Left = newNode(-10)
    root8.Right.Left = newNode(5)
    fmt.Println("Exemple 8:", levelOrder(root8)) // Expected: [[0],[-3,9],[-10,5]]
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func levelOrder(root *TreeNode) [][]int {
    if root == nil {
        return [][]int{}
    }

    result := [][]int{}
    queue := []*TreeNode{root}

    for len(queue) > 0 {
        levelSize := len(queue)
        currentLevel := []int{}

        // Traiter tous les nœuds du niveau actuel
        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]

            // Ajouter la valeur au niveau actuel
            currentLevel = append(currentLevel, node.Val)

            // Ajouter les enfants pour le niveau suivant
            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }

        // Ajouter le niveau complet au résultat
        result = append(result, currentLevel)
    }

    return result
}
```

**Simulation détaillée pour l'exemple 1 :**

```
Arbre: root = [3,9,20,null,null,15,7]
        3
       / \
      9  20
         /  \
        15   7

Initialisation:
- result = []
- queue = [3]

Niveau 0:
- levelSize = 1
- currentLevel = []
- Traiter nœud 3: currentLevel = [3]
- Ajouter enfants 9, 20: queue = [9, 20]
- result = [[3]]

Niveau 1:
- levelSize = 2
- currentLevel = []
- Traiter nœud 9: currentLevel = [9], pas d'enfants
- Traiter nœud 20: currentLevel = [9, 20]
- Ajouter enfants 15, 7: queue = [15, 7]
- result = [[3], [9, 20]]

Niveau 2:
- levelSize = 2
- currentLevel = []
- Traiter nœud 15: currentLevel = [15], pas d'enfants
- Traiter nœud 7: currentLevel = [15, 7], pas d'enfants
- queue = []
- result = [[3], [9, 20], [15, 7]]

Fin: queue est vide
Résultat: [[3], [9, 20], [15, 7]]
```

**Points clés de l'algorithme :**

1. **BFS classique** : Utilise une queue pour parcourir niveau par niveau
2. **Taille du niveau** : `levelSize = len(queue)` au début de chaque niveau
3. **Traitement par niveau** : Boucle interne pour traiter exactement un niveau
4. **Construction du résultat** : Tableau pour chaque niveau, puis ajout au résultat final

**Complexité :**
- Temps : O(n) - chaque nœud est visité exactement une fois
- Espace : O(w) où w est la largeur maximale de l'arbre (taille max de la queue)
  - Dans le pire cas (arbre équilibré), w = n/2, donc O(n)

**Pourquoi BFS pour ce problème ?**
- **Ordre naturel** : BFS visite naturellement les nœuds niveau par niveau
- **Groupement facile** : La technique "taille du niveau" groupe automatiquement les nœuds
- **Efficacité** : Pas besoin de structures de données complexes

</details>

## 🎯 Points clés à retenir

1. **Pattern BFS niveau par niveau** : `levelSize = len(queue)` pour séparer les niveaux
2. **Queue FIFO** : Garantit l'ordre de gauche à droite dans chaque niveau
3. **Gestion des enfants** : Ajouter les enfants non-nuls à la queue pour le niveau suivant
4. **Structure du résultat** : Tableau de tableaux, un par niveau

## 🚀 Exercices similaires

- [Binary Tree Right Side View](../medium/right-side-view.md) - Vue de droite de l'arbre
- [Binary Tree Zigzag Level Order Traversal](../medium/zigzag-level.md) - Parcours en zigzag
- [Average of Levels in Binary Tree](../easy/average-levels.md) - Moyenne de chaque niveau

## 🔍 Variations

**Version récursive avec DFS :**
```go
func levelOrderDFS(root *TreeNode) [][]int {
    result := [][]int{}

    var dfs func(*TreeNode, int)
    dfs = func(node *TreeNode, level int) {
        if node == nil {
            return
        }

        // Étendre result si nécessaire
        if level >= len(result) {
            result = append(result, []int{})
        }

        result[level] = append(result[level], node.Val)
        dfs(node.Left, level+1)
        dfs(node.Right, level+1)
    }

    dfs(root, 0)
    return result
}
```

**Version avec channels (Go concurrent) :**
```go
func levelOrderConcurrent(root *TreeNode) [][]int {
    if root == nil {
        return [][]int{}
    }

    type levelNode struct {
        node  *TreeNode
        level int
    }

    ch := make(chan levelNode, 100)
    levels := make(map[int][]int)

    go func() {
        ch <- levelNode{root, 0}
        close(ch)
    }()

    for ln := range ch {
        levels[ln.level] = append(levels[ln.level], ln.node.Val)
        // Logique pour ajouter les enfants...
    }

    return convertMapToSlice(levels)
}
```

**Optimisation mémoire :**
```go
// Utiliser une queue circulaire pour réutiliser la mémoire
type CircularQueue struct {
    items []*TreeNode
    head, tail, size int
}
```