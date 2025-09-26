# Exercice - Binary Tree Paths

**Niveau :** Medium
**Pattern :** DFS on Trees (avec backtracking)
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

Étant donné la racine d'un arbre binaire, retournez **tous les chemins racine-feuille** sous forme de strings.

Chaque chemin doit être représenté sous forme de string où les valeurs des nœuds sont séparées par `"->"` (par exemple : `"1->2->5"`).

Un **nœud feuille** est un nœud qui n'a pas d'enfants.

## 🔍 Exemples

**Exemple 1 :**
```
   1
 /   \
2     3
 \
  5

Input: root = [1,2,3,null,5]
Output: ["1->2->5","1->3"]
Explication: Tous les chemins racine-feuille sont : 1->2->5 et 1->3.
```

**Exemple 2 :**
```
  1

Input: root = [1]
Output: ["1"]
Explication: Il n'y a qu'un seul nœud, donc un seul chemin.
```

**Exemple 3 :**
```
    1
   / \
  2   3
 /
4

Input: root = [1,2,3,4]
Output: ["1->2->4","1->3"]
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[1, 100]`
- `-100 <= Node.val <= 100`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème de **DFS avec backtracking**. Maintenez le chemin actuel sous forme de string et ajoutez-le au résultat quand vous atteignez une feuille.

</details>

<details>
<summary>Indice 2</summary>

Vous pouvez construire le chemin de deux façons : soit en accumulant une string, soit en maintenant une liste de valeurs que vous convertissez en string à la fin.

</details>

<details>
<summary>Indice 3</summary>

Attention au format : il ne doit pas y avoir de `"->"` au début ou à la fin du chemin.

</details>

<details>
<summary>Indice 4</summary>

Alternative : passez le chemin par valeur au lieu d'utiliser le backtracking explicite.

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "strings"
    "strconv"
)

type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

func binaryTreePaths(root *TreeNode) []string {
    // Votre code ici

}

func main() {
    // Construire l'arbre de l'exemple 1
    //    1
    //  /   \
    // 2     3
    //  \
    //   5

    root := &TreeNode{Val: 1}
    root.Left = &TreeNode{Val: 2}
    root.Right = &TreeNode{Val: 3}
    root.Left.Right = &TreeNode{Val: 5}

    paths := binaryTreePaths(root)
    fmt.Println("Exemple 1 - chemins:", paths)
    // Expected: ["1->2->5" "1->3"]

    // Test avec un seul nœud
    single := &TreeNode{Val: 1}
    singlePath := binaryTreePaths(single)
    fmt.Println("Nœud unique:", singlePath)
    // Expected: ["1"]

    // Construire l'arbre de l'exemple 3
    //     1
    //    / \
    //   2   3
    //  /
    // 4

    root2 := &TreeNode{Val: 1}
    root2.Left = &TreeNode{Val: 2}
    root2.Right = &TreeNode{Val: 3}
    root2.Left.Left = &TreeNode{Val: 4}

    paths2 := binaryTreePaths(root2)
    fmt.Println("Exemple 3 - chemins:", paths2)
    // Expected: ["1->2->4" "1->3"]
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func binaryTreePaths(root *TreeNode) []string {
    var result []string
    var currentPath []string

    var dfs func(*TreeNode)
    dfs = func(node *TreeNode) {
        if node == nil {
            return
        }

        // Ajouter le nœud actuel au chemin
        currentPath = append(currentPath, strconv.Itoa(node.Val))

        // Si c'est une feuille, ajouter le chemin au résultat
        if node.Left == nil && node.Right == nil {
            result = append(result, strings.Join(currentPath, "->"))
        } else {
            // Explorer les enfants
            dfs(node.Left)
            dfs(node.Right)
        }

        // Backtracking: retirer le nœud du chemin
        currentPath = currentPath[:len(currentPath)-1]
    }

    dfs(root)
    return result
}
```

**Version avec passage par valeur (plus simple) :**

```go
func binaryTreePaths(root *TreeNode) []string {
    var result []string

    var dfs func(*TreeNode, string)
    dfs = func(node *TreeNode, path string) {
        if node == nil {
            return
        }

        // Construire le nouveau chemin
        if path == "" {
            path = strconv.Itoa(node.Val)
        } else {
            path += "->" + strconv.Itoa(node.Val)
        }

        // Si c'est une feuille, ajouter le chemin
        if node.Left == nil && node.Right == nil {
            result = append(result, path)
            return
        }

        // Explorer les enfants avec le nouveau chemin
        dfs(node.Left, path)
        dfs(node.Right, path)
    }

    dfs(root, "")
    return result
}
```

**Version avec slice de valeurs :**

```go
func binaryTreePaths(root *TreeNode) []string {
    var result []string

    var dfs func(*TreeNode, []int)
    dfs = func(node *TreeNode, path []int) {
        if node == nil {
            return
        }

        // Ajouter le nœud actuel (crée une nouvelle slice)
        newPath := append(path, node.Val)

        // Si c'est une feuille, convertir et ajouter
        if node.Left == nil && node.Right == nil {
            var pathStr []string
            for _, val := range newPath {
                pathStr = append(pathStr, strconv.Itoa(val))
            }
            result = append(result, strings.Join(pathStr, "->"))
            return
        }

        // Explorer les enfants
        dfs(node.Left, newPath)
        dfs(node.Right, newPath)
    }

    dfs(root, []int{})
    return result
}
```

**Version itérative :**

```go
func binaryTreePaths(root *TreeNode) []string {
    if root == nil {
        return []string{}
    }

    var result []string

    type pair struct {
        node *TreeNode
        path string
    }

    stack := []pair{{root, strconv.Itoa(root.Val)}}

    for len(stack) > 0 {
        current := stack[len(stack)-1]
        stack = stack[:len(stack)-1]

        node := current.node
        path := current.path

        // Si c'est une feuille, ajouter le chemin
        if node.Left == nil && node.Right == nil {
            result = append(result, path)
            continue
        }

        // Ajouter les enfants à la stack
        if node.Right != nil {
            stack = append(stack, pair{node.Right, path + "->" + strconv.Itoa(node.Right.Val)})
        }
        if node.Left != nil {
            stack = append(stack, pair{node.Left, path + "->" + strconv.Itoa(node.Left.Val)})
        }
    }

    return result
}
```

**Simulation pour l'arbre d'exemple :**

```
        1
      /   \
     2     3
      \
       5

dfs(1, ""):
  path = "1"
  1 n'est pas une feuille

  dfs(2, "1"):
    path = "1->2"
    2 n'est pas une feuille

    dfs(nil, "1->2"): return (pas d'enfant gauche)
    dfs(5, "1->2"):
      path = "1->2->5"
      5 est une feuille → ajouter "1->2->5" au résultat

  dfs(3, "1"):
    path = "1->3"
    3 est une feuille → ajouter "1->3" au résultat

Résultat: ["1->2->5", "1->3"]
```

**Complexité :**
- Temps : O(N * H) où N = nombre de nœuds, H = hauteur
  - Pour chaque feuille, on construit un chemin de longueur H
- Espace : O(N * H) pour stocker tous les chemins + O(H) pour la pile de récursion

**Points critiques :**
1. **Format du chemin** : Utiliser `"->"` comme séparateur
2. **Condition de feuille** : `node.Left == nil && node.Right == nil`
3. **Backtracking** : Si vous modifiez le chemin en place, pensez à le restaurer
4. **Conversion string** : Utiliser `strconv.Itoa()` pour les entiers

</details>

## 🎯 Points clés à retenir

1. **DFS avec backtracking** : Explorer tous les chemins puis revenir en arrière
2. **Construction de chemins** : Plusieurs approches possibles (string, slice, etc.)
3. **Format de sortie** : Respecter exactement le format demandé
4. **Condition de feuille** : Vérifier explicitement les deux enfants

## 🚀 Exercices similaires

- [Path Sum](../easy/path-sum.md) - Chemins avec somme spécifique
- [Path Sum II](../medium/path-sum-ii.md) - Chemins avec somme + valeurs
- [Sum Root to Leaf Numbers](../medium/sum-root-leaf.md) - Interpréter les chemins comme nombres

## 🔍 Variations

**Chemins avec séparateur personnalisé :**
```go
func binaryTreePathsCustom(root *TreeNode, separator string) []string {
    var result []string

    var dfs func(*TreeNode, string)
    dfs = func(node *TreeNode, path string) {
        if node == nil {
            return
        }

        if path == "" {
            path = strconv.Itoa(node.Val)
        } else {
            path += separator + strconv.Itoa(node.Val)
        }

        if node.Left == nil && node.Right == nil {
            result = append(result, path)
            return
        }

        dfs(node.Left, path)
        dfs(node.Right, path)
    }

    dfs(root, "")
    return result
}
```

**Compter le nombre de chemins :**
```go
func countPaths(root *TreeNode) int {
    if root == nil {
        return 0
    }

    if root.Left == nil && root.Right == nil {
        return 1
    }

    return countPaths(root.Left) + countPaths(root.Right)
}
```