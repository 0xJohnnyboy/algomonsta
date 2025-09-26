# Exercice - Serialize and Deserialize Binary Tree

**Niveau :** Hard
**Pattern :** DFS on Trees (sérialisation et reconstruction)
**Retour au cours :** [DFS on Trees Pattern](../../courses/05-dfs-tree.md)

## 📝 Énoncé

La **sérialisation** est le processus de conversion d'une structure de données ou d'un objet en une séquence de bits afin qu'il puisse être stocké dans un fichier ou un tampon mémoire, ou transmis à travers une connexion réseau pour être reconstruit plus tard dans le même ou un autre environnement informatique.

Concevez un algorithme pour **sérialiser et désérialiser un arbre binaire**. Il n'y a aucune restriction sur la façon dont votre algorithme de sérialisation/désérialisation doit fonctionner. Vous devez juste vous assurer qu'un arbre binaire peut être sérialisé en une chaîne et que cette chaîne peut être désérialisée vers la structure d'arbre binaire originale.

**Clarification :** Le format d'entrée est le même que celui de la sérialisation LeetCode d'un arbre binaire. Vous n'êtes pas obligé de suivre ce format, vous pouvez donc être créatif et proposer vos propres algorithmes différents.

## 🔍 Exemples

**Exemple 1 :**
```
    1
   / \
  2   3
     / \
    4   5

Input: root = [1,2,3,null,null,4,5]
Sérialisation possible: "1,2,null,null,3,4,null,null,5,null,null"
Désérialisation: Reconstruit l'arbre original
```

**Exemple 2 :**
```
Input: root = []
Output: []
```

**Exemple 3 :**
```
Input: root = [1]
Sérialisation possible: "1,null,null"
```

## 🎯 Contraintes

- Le nombre de nœuds dans l'arbre est dans la plage `[0, 10^4]`
- `-1000 <= Node.val <= 1000`

## 💡 Indices

<details>
<summary>Indice 1</summary>

Utilisez le **parcours préfixe (preorder)** pour la sérialisation. C'est naturel car on traite d'abord la racine, puis les sous-arbres.

</details>

<details>
<summary>Indice 2</summary>

Pour représenter les nœuds `nil`, utilisez un marqueur spécial comme `"null"` ou `"#"`.

</details>

<details>
<summary>Indice 3</summary>

Pour la désérialisation, utilisez un **index global** ou une **queue** pour traiter séquentiellement les valeurs sérialisées.

</details>

<details>
<summary>Indice 4</summary>

La sérialisation et la désérialisation doivent utiliser le **même ordre de parcours** pour être cohérentes.

</details>

## 🔨 Template de solution

```go
package main

import (
    "fmt"
    "strconv"
    "strings"
)

type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

type Codec struct {

}

func Constructor() Codec {
    return Codec{}
}

// Serializes a tree to a single string.
func (this *Codec) serialize(root *TreeNode) string {
    // Votre code ici

}

// Deserializes your encoded data to tree.
func (this *Codec) deserialize(data string) *TreeNode {
    // Votre code ici

}

// Fonction helper pour afficher l'arbre (pour les tests)
func printInorder(root *TreeNode) []int {
    if root == nil {
        return []int{}
    }
    result := []int{}
    result = append(result, printInorder(root.Left)...)
    result = append(result, root.Val)
    result = append(result, printInorder(root.Right)...)
    return result
}

func main() {
    codec := Constructor()

    // Exemple 1: Arbre complet
    //     1
    //    / \
    //   2   3
    //      / \
    //     4   5

    root1 := &TreeNode{Val: 1}
    root1.Left = &TreeNode{Val: 2}
    root1.Right = &TreeNode{Val: 3}
    root1.Right.Left = &TreeNode{Val: 4}
    root1.Right.Right = &TreeNode{Val: 5}

    fmt.Println("Arbre original (inorder):", printInorder(root1))

    serialized1 := codec.serialize(root1)
    fmt.Println("Sérialisé:", serialized1)

    deserialized1 := codec.deserialize(serialized1)
    fmt.Println("Désérialisé (inorder):", printInorder(deserialized1))

    // Exemple 2: Arbre vide
    serialized2 := codec.serialize(nil)
    fmt.Println("Arbre vide sérialisé:", serialized2)

    deserialized2 := codec.deserialize(serialized2)
    fmt.Println("Arbre vide désérialisé:", deserialized2)

    // Exemple 3: Nœud unique
    single := &TreeNode{Val: 42}
    serialized3 := codec.serialize(single)
    fmt.Println("Nœud unique sérialisé:", serialized3)

    deserialized3 := codec.deserialize(serialized3)
    fmt.Println("Nœud unique désérialisé:", printInorder(deserialized3))
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

**Approche 1 : Preorder avec séparateurs**

```go
type Codec struct {}

func Constructor() Codec {
    return Codec{}
}

func (this *Codec) serialize(root *TreeNode) string {
    var result []string

    var preorder func(*TreeNode)
    preorder = func(node *TreeNode) {
        if node == nil {
            result = append(result, "null")
            return
        }

        result = append(result, strconv.Itoa(node.Val))
        preorder(node.Left)
        preorder(node.Right)
    }

    preorder(root)
    return strings.Join(result, ",")
}

func (this *Codec) deserialize(data string) *TreeNode {
    if data == "" {
        return nil
    }

    values := strings.Split(data, ",")
    index := 0

    var buildTree func() *TreeNode
    buildTree = func() *TreeNode {
        if index >= len(values) || values[index] == "null" {
            index++
            return nil
        }

        val, _ := strconv.Atoi(values[index])
        index++

        node := &TreeNode{Val: val}
        node.Left = buildTree()
        node.Right = buildTree()

        return node
    }

    return buildTree()
}
```

**Approche 2 : Avec queue pour désérialisation**

```go
type Codec struct {}

func Constructor() Codec {
    return Codec{}
}

func (this *Codec) serialize(root *TreeNode) string {
    if root == nil {
        return "null"
    }

    return strconv.Itoa(root.Val) + "," +
           this.serialize(root.Left) + "," +
           this.serialize(root.Right)
}

func (this *Codec) deserialize(data string) *TreeNode {
    values := strings.Split(data, ",")
    queue := make([]string, len(values))
    copy(queue, values)

    return this.deserializeHelper(&queue)
}

func (this *Codec) deserializeHelper(queue *[]string) *TreeNode {
    if len(*queue) == 0 {
        return nil
    }

    val := (*queue)[0]
    *queue = (*queue)[1:]

    if val == "null" {
        return nil
    }

    nodeVal, _ := strconv.Atoi(val)
    node := &TreeNode{Val: nodeVal}

    node.Left = this.deserializeHelper(queue)
    node.Right = this.deserializeHelper(queue)

    return node
}
```

**Approche 3 : Level-order (BFS)**

```go
type Codec struct {}

func Constructor() Codec {
    return Codec{}
}

func (this *Codec) serialize(root *TreeNode) string {
    if root == nil {
        return ""
    }

    var result []string
    queue := []*TreeNode{root}

    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:]

        if node != nil {
            result = append(result, strconv.Itoa(node.Val))
            queue = append(queue, node.Left)
            queue = append(queue, node.Right)
        } else {
            result = append(result, "null")
        }
    }

    return strings.Join(result, ",")
}

func (this *Codec) deserialize(data string) *TreeNode {
    if data == "" {
        return nil
    }

    values := strings.Split(data, ",")
    if values[0] == "null" {
        return nil
    }

    rootVal, _ := strconv.Atoi(values[0])
    root := &TreeNode{Val: rootVal}
    queue := []*TreeNode{root}

    i := 1
    for len(queue) > 0 && i < len(values) {
        node := queue[0]
        queue = queue[1:]

        // Traiter l'enfant gauche
        if i < len(values) && values[i] != "null" {
            leftVal, _ := strconv.Atoi(values[i])
            node.Left = &TreeNode{Val: leftVal}
            queue = append(queue, node.Left)
        }
        i++

        // Traiter l'enfant droit
        if i < len(values) && values[i] != "null" {
            rightVal, _ := strconv.Atoi(values[i])
            node.Right = &TreeNode{Val: rightVal}
            queue = append(queue, node.Right)
        }
        i++
    }

    return root
}
```

**Simulation de sérialisation (preorder) :**

```
        1
       / \
      2   3
         / \
        4   5

serialize(1):
  result = ["1"]
  serialize(2):
    result = ["1", "2"]
    serialize(null): result = ["1", "2", "null"]
    serialize(null): result = ["1", "2", "null", "null"]
  serialize(3):
    result = ["1", "2", "null", "null", "3"]
    serialize(4):
      result = ["1", "2", "null", "null", "3", "4"]
      serialize(null): result = ["1", "2", "null", "null", "3", "4", "null"]
      serialize(null): result = ["1", "2", "null", "null", "3", "4", "null", "null"]
    serialize(5):
      result = ["1", "2", "null", "null", "3", "4", "null", "null", "5"]
      serialize(null): result = [..., "5", "null"]
      serialize(null): result = [..., "5", "null", "null"]

Résultat: "1,2,null,null,3,4,null,null,5,null,null"
```

**Simulation de désérialisation :**

```
data = "1,2,null,null,3,4,null,null,5,null,null"
values = ["1", "2", "null", "null", "3", "4", "null", "null", "5", "null", "null"]
index = 0

buildTree():
  values[0] = "1" → node = TreeNode{1}, index = 1

  node.Left = buildTree():
    values[1] = "2" → node = TreeNode{2}, index = 2

    node.Left = buildTree():
      values[2] = "null" → return nil, index = 3

    node.Right = buildTree():
      values[3] = "null" → return nil, index = 4

    return TreeNode{2}

  node.Right = buildTree():
    values[4] = "3" → node = TreeNode{3}, index = 5

    node.Left = buildTree():
      values[5] = "4" → ... → return TreeNode{4}

    node.Right = buildTree():
      values[8] = "5" → ... → return TreeNode{5}

    return TreeNode{3}

  return TreeNode{1} avec structure complète
```

**Complexité :**
- Temps : O(N) pour sérialisation et désérialisation
- Espace : O(N) pour stocker la représentation + O(H) pour la pile de récursion

**Points critiques :**
1. **Cohérence** : Même ordre de parcours pour sérialisation et désérialisation
2. **Gestion des nœuds null** : Marqueur explicite nécessaire
3. **Parsing** : Attention à la gestion des index/queue
4. **Format** : Choisir un séparateur qui ne peut pas apparaître dans les valeurs

</details>

## 🎯 Points clés à retenir

1. **Ordre de parcours** : Doit être cohérent entre sérialisation et désérialisation
2. **Marqueurs null** : Essentiels pour reconstruire la structure exacte
3. **État global** : Utiliser index ou queue pour suivre la position
4. **Format de données** : Choisir un format non-ambigu

## 🚀 Exercices similaires

- [Construct Binary Tree from Preorder and Inorder](../medium/construct-tree.md) - Construction avec deux parcours
- [Verify Preorder Serialization](../medium/verify-preorder.md) - Valider une sérialisation
- [Encode and Decode Strings](../medium/encode-decode.md) - Principe similaire pour strings

## 🔍 Variations

**Sérialisation compacte (sans nulls redondants) :**
```go
func (this *Codec) serializeCompact(root *TreeNode) string {
    if root == nil {
        return ""
    }

    var result []string
    queue := []*TreeNode{root}

    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:]

        if node != nil {
            result = append(result, strconv.Itoa(node.Val))
            queue = append(queue, node.Left)
            queue = append(queue, node.Right)
        } else {
            result = append(result, "#")
        }
    }

    // Supprimer les null à la fin
    for len(result) > 0 && result[len(result)-1] == "#" {
        result = result[:len(result)-1]
    }

    return strings.Join(result, ",")
}
```

**Sérialisation avec compression :**
```go
func (this *Codec) serializeCompressed(root *TreeNode) string {
    serialized := this.serialize(root)

    // Compresser les séquences répétées de "null"
    compressed := strings.ReplaceAll(serialized, "null,null,null", "3null")
    compressed = strings.ReplaceAll(compressed, "null,null", "2null")

    return compressed
}
```