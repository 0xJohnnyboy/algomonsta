# Cours 5 - DFS on Trees Pattern

## 🎯 Quand utiliser ce pattern ?

Le **DFS (Depth-First Search)** sur les arbres est essentiel pour :
- **Parcourir tous les nœuds** d'un arbre
- **Calculer des propriétés** (hauteur, somme, diamètre)
- **Chercher des chemins** ou des valeurs spécifiques
- **Valider des propriétés** d'arbres (BST, balanced, etc.)
- **Transformer ou construire** des arbres

## 🔍 Comment identifier ce pattern

**Mots-clés dans l'énoncé :**
- "binary tree", "tree traversal"
- "path sum", "root to leaf"
- "maximum depth", "height of tree"
- "validate binary search tree"
- "invert tree", "serialize tree"

**Structure typique :** Problèmes avec `TreeNode` en paramètre.

## 🌳 Structure d'arbre en Go

```go
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}
```

## 📋 Templates de base

### Template 1 - DFS Récursif Simple

```go
func dfs(root *TreeNode) returnType {
    // Cas de base
    if root == nil {
        return baseValue
    }
    
    // Traiter le nœud actuel (Pre-order)
    // currentResult := process(root.Val)
    
    // Explorer récursivement
    leftResult := dfs(root.Left)
    rightResult := dfs(root.Right)
    
    // Combiner les résultats
    return combine(currentResult, leftResult, rightResult)
}
```

### Template 2 - DFS avec état global

```go
func dfsWithGlobal(root *TreeNode) returnType {
    var result returnType
    
    var helper func(*TreeNode)
    helper = func(node *TreeNode) {
        if node == nil {
            return
        }
        
        // Traiter le nœud
        // Modifier result si nécessaire
        
        helper(node.Left)
        helper(node.Right)
    }
    
    helper(root)
    return result
}
```

### Template 3 - DFS avec chemin

```go
func dfsWithPath(root *TreeNode, target int) bool {
    var dfs func(*TreeNode, []int) bool
    dfs = func(node *TreeNode, path []int) bool {
        if node == nil {
            return false
        }
        
        // Ajouter le nœud au chemin
        path = append(path, node.Val)
        
        // Vérifier si c'est une feuille et si condition remplie
        if node.Left == nil && node.Right == nil {
            // Traiter le chemin complet
            return checkCondition(path, target)
        }
        
        // Explorer les enfants
        return dfs(node.Left, path) || dfs(node.Right, path)
    }
    
    return dfs(root, []int{})
}
```

## 🚀 Exemple 1 - Maximum Depth of Binary Tree

**Problème :** Trouver la profondeur maximale d'un arbre binaire.

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
    if a > b { return a }
    return b
}
```

## 🚀 Exemple 2 - Path Sum

**Problème :** Vérifier s'il existe un chemin racine-feuille avec une somme donnée.

```go
func hasPathSum(root *TreeNode, targetSum int) bool {
    if root == nil {
        return false
    }
    
    // Si c'est une feuille, vérifier la somme
    if root.Left == nil && root.Right == nil {
        return root.Val == targetSum
    }
    
    // Réduire targetSum et explorer les enfants
    remaining := targetSum - root.Val
    return hasPathSum(root.Left, remaining) || hasPathSum(root.Right, remaining)
}
```

## 🚀 Exemple 3 - Binary Tree Paths

**Problème :** Retourner tous les chemins racine-feuille.

```go
func binaryTreePaths(root *TreeNode) []string {
    if root == nil {
        return []string{}
    }
    
    var result []string
    
    var dfs func(*TreeNode, string)
    dfs = func(node *TreeNode, path string) {
        if node == nil {
            return
        }
        
        // Ajouter le nœud actuel au chemin
        if path == "" {
            path = fmt.Sprintf("%d", node.Val)
        } else {
            path = fmt.Sprintf("%s->%d", path, node.Val)
        }
        
        // Si c'est une feuille, ajouter le chemin au résultat
        if node.Left == nil && node.Right == nil {
            result = append(result, path)
            return
        }
        
        // Explorer les enfants
        dfs(node.Left, path)
        dfs(node.Right, path)
    }
    
    dfs(root, "")
    return result
}
```

## 🚀 Exemple 4 - Validate Binary Search Tree

**Problème :** Vérifier si un arbre est un BST valide.

```go
import "math"

func isValidBST(root *TreeNode) bool {
    return validate(root, math.MinInt64, math.MaxInt64)
}

func validate(node *TreeNode, minVal, maxVal int) bool {
    if node == nil {
        return true
    }
    
    // Vérifier les contraintes BST
    if node.Val <= minVal || node.Val >= maxVal {
        return false
    }
    
    // Pour le sous-arbre gauche: max devient node.Val
    // Pour le sous-arbre droit: min devient node.Val
    return validate(node.Left, minVal, node.Val) && 
           validate(node.Right, node.Val, maxVal)
}
```

## 🚀 Exemple 5 - Diameter of Binary Tree

**Problème :** Trouver le diamètre (plus long chemin entre deux nœuds).

```go
func diameterOfBinaryTree(root *TreeNode) int {
    maxDiameter := 0
    
    var height func(*TreeNode) int
    height = func(node *TreeNode) int {
        if node == nil {
            return 0
        }
        
        leftHeight := height(node.Left)
        rightHeight := height(node.Right)
        
        // Le diamètre passant par ce nœud
        currentDiameter := leftHeight + rightHeight
        maxDiameter = max(maxDiameter, currentDiameter)
        
        // Retourner la hauteur de ce sous-arbre
        return 1 + max(leftHeight, rightHeight)
    }
    
    height(root)
    return maxDiameter
}
```

## 📊 Complexité

- **Temporelle :** O(n) - visite chaque nœud une fois
- **Spatiale :** O(h) - pile de récursion, où h est la hauteur de l'arbre
  - Balanced tree: O(log n)
  - Skewed tree: O(n)

## ⚠️ Pièges courants

1. **Cas de base manqué** - Toujours vérifier `if root == nil`
2. **Feuilles vs nœuds internes** - Bien distinguer les deux cas
3. **Stack overflow** - Arbres très déséquilibrés peuvent causer des problèmes
4. **État partagé** - Attention aux variables globales modifiées par plusieurs appels

## 🔄 Types de parcours DFS

### 1. Pre-order (Racine, Gauche, Droite)
```go
func preorder(root *TreeNode) []int {
    if root == nil {
        return []int{}
    }
    
    result := []int{root.Val}
    result = append(result, preorder(root.Left)...)
    result = append(result, preorder(root.Right)...)
    return result
}
```

### 2. In-order (Gauche, Racine, Droite)
```go
func inorder(root *TreeNode) []int {
    if root == nil {
        return []int{}
    }
    
    result := []int{}
    result = append(result, inorder(root.Left)...)
    result = append(result, root.Val)
    result = append(result, inorder(root.Right)...)
    return result
}
```

### 3. Post-order (Gauche, Droite, Racine)
```go
func postorder(root *TreeNode) []int {
    if root == nil {
        return []int{}
    }
    
    result := []int{}
    result = append(result, postorder(root.Left)...)
    result = append(result, postorder(root.Right)...)
    result = append(result, root.Val)
    return result
}
```

## 🎭 Patterns spécialisés

### Top-Down vs Bottom-Up

**Top-Down :** Passer des informations de parent vers enfant
```go
func topDown(root *TreeNode, parentInfo int) returnType {
    if root == nil {
        return baseCase
    }
    
    // Utiliser parentInfo + root.Val
    newInfo := computeNewInfo(parentInfo, root.Val)
    
    left := topDown(root.Left, newInfo)
    right := topDown(root.Right, newInfo)
    
    return combineResults(left, right)
}
```

**Bottom-Up :** Collecter des informations des enfants vers parent
```go
func bottomUp(root *TreeNode) returnType {
    if root == nil {
        return baseCase
    }
    
    left := bottomUp(root.Left)
    right := bottomUp(root.Right)
    
    // Utiliser les informations des enfants
    return computeFromChildren(root.Val, left, right)
}
```

### Mémorisation avec Map
```go
func dfsWithMemo(root *TreeNode) int {
    memo := make(map[*TreeNode]int)
    
    var dfs func(*TreeNode) int
    dfs = func(node *TreeNode) int {
        if node == nil {
            return 0
        }
        
        if val, exists := memo[node]; exists {
            return val
        }
        
        result := compute(node, dfs(node.Left), dfs(node.Right))
        memo[node] = result
        return result
    }
    
    return dfs(root)
}
```

## 🏃‍♂️ Exercices pratiques

### Easy
- [Maximum Depth of Binary Tree](../exercises/dfs-tree/easy/max-depth.md)
- [Invert Binary Tree](../exercises/dfs-tree/easy/invert-tree.md)
- [Path Sum](../exercises/dfs-tree/easy/path-sum.md)
- [Same Tree](../exercises/dfs-tree/easy/same-tree.md)

### Medium
- [Path Sum II](../exercises/dfs-tree/medium/path-sum-ii.md)
- [Binary Tree Paths](../exercises/dfs-tree/medium/tree-paths.md)
- [Validate Binary Search Tree](../exercises/dfs-tree/medium/validate-bst.md)
- [Diameter of Binary Tree](../exercises/dfs-tree/medium/diameter.md)
- [Lowest Common Ancestor](../exercises/dfs-tree/medium/lca.md)

### Hard
- [Binary Tree Maximum Path Sum](../exercises/dfs-tree/hard/max-path-sum.md)
- [Serialize and Deserialize Binary Tree](../exercises/dfs-tree/hard/serialize.md)

## 🧠 Stratégie de résolution

1. **Identifier le type de problème** :
   - Calcul de propriété ? → Bottom-up
   - Propagation d'information ? → Top-down
   - Recherche de chemin ? → DFS avec backtracking

2. **Déterminer le cas de base** : généralement `if root == nil`

3. **Choisir le bon parcours** :
   - In-order pour BST
   - Post-order pour calculer des propriétés
   - Pre-order pour copier/transformer

4. **Gérer l'état** :
   - Variables locales pour état temporaire
   - Paramètres pour transmettre l'information
   - Variables globales avec prudence

## ➡️ Cours suivant

[Cours 6 - BFS on Graphs Pattern](06-bfs-graph.md) - Pour explorer les graphes niveau par niveau.
