# Exercice - Same Tree

**Niveau :** Easy
**Pattern :** DFS on Trees
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

Étant donné les racines de deux arbres binaires `p` et `q`, écrivez une fonction pour vérifier s'ils sont **identiques**.

Deux arbres binaires sont considérés comme identiques s'ils sont structurellement identiques et que les nœuds ont les mêmes valeurs.

## 🔍 Exemples

**Exemple 1 :**
```
   p:      q:
   1       1
  / \     / \
 2   3   2   3

Input: p = [1,2,3], q = [1,2,3]
Output: true
Explication: Les deux arbres ont la même structure et les mêmes valeurs.
```

**Exemple 2 :**
```
   p:    q:
   1     1
  /       \
 2         2

Input: p = [1,2], q = [1,null,2]
Output: false
Explication: Les arbres ont des structures différentes.
```

**Exemple 3 :**
```
   p:      q:
   1       1
  / \     / \
 2   1   1   2

Input: p = [1,2,1], q = [1,1,2]
Output: false
Explication: Les arbres ont la même structure mais des valeurs différentes.
```

## 🎯 Contraintes

- Le nombre de nœuds dans les deux arbres est dans la plage `[0, 100]`
- `-10^4 <= Node.val <= 10^4`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème de **DFS récursive**. Comparez les nœuds actuels, puis récursivement les sous-arbres gauche et droit.

</details>

<details>
<summary>Indice 2</summary>

Cas de base : si les deux nœuds sont `nil`, ils sont identiques. Si un seul est `nil`, ils ne le sont pas.

</details>

<details>
<summary>Indice 3</summary>

Pour que deux arbres soient identiques : `p.Val == q.Val` ET `isSameTree(p.Left, q.Left)` ET `isSameTree(p.Right, q.Right)`

</details>

<details>
<summary>Indice 4</summary>

Vous pouvez aussi résoudre ce problème de manière itérative avec deux stacks parallèles.

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

func isSameTree(p *TreeNode, q *TreeNode) bool {
    // Votre code ici

}

func main() {
    // Exemple 1: Arbres identiques
    //    1       1
    //   / \     / \
    //  2   3   2   3

    p1 := &TreeNode{Val: 1}
    p1.Left = &TreeNode{Val: 2}
    p1.Right = &TreeNode{Val: 3}

    q1 := &TreeNode{Val: 1}
    q1.Left = &TreeNode{Val: 2}
    q1.Right = &TreeNode{Val: 3}

    fmt.Println("Exemple 1 - identiques:", isSameTree(p1, q1))
    // Expected: true

    // Exemple 2: Structures différentes
    //   1     1
    //  /       \
    // 2         2

    p2 := &TreeNode{Val: 1}
    p2.Left = &TreeNode{Val: 2}

    q2 := &TreeNode{Val: 1}
    q2.Right = &TreeNode{Val: 2}

    fmt.Println("Exemple 2 - structures différentes:", isSameTree(p2, q2))
    // Expected: false

    // Exemple 3: Valeurs différentes
    //   1       1
    //  / \     / \
    // 2   1   1   2

    p3 := &TreeNode{Val: 1}
    p3.Left = &TreeNode{Val: 2}
    p3.Right = &TreeNode{Val: 1}

    q3 := &TreeNode{Val: 1}
    q3.Left = &TreeNode{Val: 1}
    q3.Right = &TreeNode{Val: 2}

    fmt.Println("Exemple 3 - valeurs différentes:", isSameTree(p3, q3))
    // Expected: false

    // Test avec arbres vides
    fmt.Println("Arbres vides:", isSameTree(nil, nil))
    // Expected: true

    // Test avec un arbre vide et un non-vide
    single := &TreeNode{Val: 1}
    fmt.Println("Un vide, un non-vide:", isSameTree(nil, single))
    // Expected: false
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func isSameTree(p *TreeNode, q *TreeNode) bool {
    // Si les deux sont nil, ils sont identiques
    if p == nil && q == nil {
        return true
    }

    // Si un seul est nil, ils ne sont pas identiques
    if p == nil || q == nil {
        return false
    }

    // Si les valeurs sont différentes, ils ne sont pas identiques
    if p.Val != q.Val {
        return false
    }

    // Vérifier récursivement les sous-arbres
    return isSameTree(p.Left, q.Left) && isSameTree(p.Right, q.Right)
}
```

**Version plus concise :**

```go
func isSameTree(p *TreeNode, q *TreeNode) bool {
    if p == nil && q == nil {
        return true
    }
    if p == nil || q == nil || p.Val != q.Val {
        return false
    }
    return isSameTree(p.Left, q.Left) && isSameTree(p.Right, q.Right)
}
```

**Version itérative avec stack :**

```go
func isSameTree(p *TreeNode, q *TreeNode) bool {
    stack := [][2]*TreeNode{{p, q}}

    for len(stack) > 0 {
        nodes := stack[len(stack)-1]
        stack = stack[:len(stack)-1]

        p, q := nodes[0], nodes[1]

        // Si les deux sont nil, continuer
        if p == nil && q == nil {
            continue
        }

        // Si un seul est nil ou valeurs différentes
        if p == nil || q == nil || p.Val != q.Val {
            return false
        }

        // Ajouter les enfants à la stack
        stack = append(stack, [2]*TreeNode{p.Left, q.Left})
        stack = append(stack, [2]*TreeNode{p.Right, q.Right})
    }

    return true
}
```

**Version itérative avec queue (BFS) :**

```go
func isSameTree(p *TreeNode, q *TreeNode) bool {
    queue := [][2]*TreeNode{{p, q}}

    for len(queue) > 0 {
        nodes := queue[0]
        queue = queue[1:]

        p, q := nodes[0], nodes[1]

        // Si les deux sont nil, continuer
        if p == nil && q == nil {
            continue
        }

        // Si un seul est nil ou valeurs différentes
        if p == nil || q == nil || p.Val != q.Val {
            return false
        }

        // Ajouter les enfants à la queue
        queue = append(queue, [2]*TreeNode{p.Left, q.Left})
        queue = append(queue, [2]*TreeNode{p.Right, q.Right})
    }

    return true
}
```

**Simulation pour l'exemple 1 :**

```
isSameTree(p=1, q=1):
  p ≠ nil, q ≠ nil, p.Val = q.Val = 1 ✓

  isSameTree(p.Left=2, q.Left=2):
    p ≠ nil, q ≠ nil, p.Val = q.Val = 2 ✓

    isSameTree(p.Left=nil, q.Left=nil): return true ✓
    isSameTree(p.Right=nil, q.Right=nil): return true ✓

    return true && true = true ✓

  isSameTree(p.Right=3, q.Right=3):
    p ≠ nil, q ≠ nil, p.Val = q.Val = 3 ✓

    isSameTree(p.Left=nil, q.Left=nil): return true ✓
    isSameTree(p.Right=nil, q.Right=nil): return true ✓

    return true && true = true ✓

  return true && true = true ✓

Résultat: true
```

**Simulation pour l'exemple 2 :**

```
isSameTree(p=1, q=1):
  p ≠ nil, q ≠ nil, p.Val = q.Val = 1 ✓

  isSameTree(p.Left=2, q.Left=nil):
    p ≠ nil, q = nil
    return false ✗

Résultat: false (pas besoin de vérifier le sous-arbre droit)
```

**Complexité :**
- Temps : O(min(N, M)) où N et M sont les nombres de nœuds dans chaque arbre
  - Dans le meilleur cas (différence trouvée rapidement) : O(1)
  - Dans le pire cas (arbres identiques) : O(N)
- Espace : O(min(H1, H2)) où H1 et H2 sont les hauteurs des arbres (pile de récursion)

**Points critiques :**
1. **Gestion des nœuds null** : Vérifier tous les cas possibles
2. **Court-circuit AND** : Dès qu'une différence est trouvée, retourner `false`
3. **Comparaison simultanée** : Toujours comparer les nœuds correspondants
4. **Ordre des vérifications** : D'abord null, puis valeurs, puis récursion

</details>

## 🎯 Points clés à retenir

1. **DFS simultanée** : Parcourir les deux arbres en parallèle
2. **Gestion des cas null** : Bien gérer tous les cas possibles
3. **Court-circuit** : Arrêter dès qu'une différence est trouvée
4. **Comparaison complète** : Structure ET valeurs doivent correspondre

## 🚀 Exercices similaires

- [Symmetric Tree](../easy/symmetric-tree.md) - Vérifier si un arbre est symétrique
- [Subtree of Another Tree](../easy/subtree.md) - Vérifier si un arbre est sous-arbre d'un autre
- [Invert Binary Tree](../easy/invert-tree.md) - Inverser un arbre

## 🔍 Variations

**Arbres similaires (même structure, valeurs différentes autorisées) :**
```go
func isSameStructure(p *TreeNode, q *TreeNode) bool {
    if p == nil && q == nil {
        return true
    }
    if p == nil || q == nil {
        return false
    }
    return isSameStructure(p.Left, q.Left) && isSameStructure(p.Right, q.Right)
}
```

**Compter les différences entre deux arbres :**
```go
func countDifferences(p *TreeNode, q *TreeNode) int {
    if p == nil && q == nil {
        return 0
    }
    if p == nil || q == nil {
        return 1
    }

    diff := 0
    if p.Val != q.Val {
        diff = 1
    }

    return diff + countDifferences(p.Left, q.Left) + countDifferences(p.Right, q.Right)
}
```