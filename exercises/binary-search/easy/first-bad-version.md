# Exercice - First Bad Version

**Niveau :** Easy
**Pattern :** Binary Search with API
**Retour au cours :** [Binary Search Pattern](../../courses/03-binary-search.md)

## 📝 Énoncé

Vous êtes chef de produit et supervisez actuellement une équipe pour expédier un produit. Malheureusement, la dernière version de votre produit échoue au contrôle qualité. Puisque chaque version est développée à partir de la version précédente, toutes les versions après une mauvaise version sont également mauvaises.

Supposons que vous avez `n` versions `[1, 2, ..., n]` et vous voulez trouver la première mauvaise qui fait que toutes les suivantes sont également mauvaises.

Vous disposez d'une API `isBadVersion(version)` qui retourne si `version` est mauvaise. Implémentez une fonction pour trouver la première mauvaise version. Vous devez minimiser le nombre d'appels à l'API.

## 🔍 Exemples

**Exemple 1 :**
```
Input: n = 5, bad = 4
Output: 4
Explication:
isBadVersion(3) = false
isBadVersion(5) = true
isBadVersion(4) = true
Donc 4 est la première mauvaise version
```

**Exemple 2 :**
```
Input: n = 1, bad = 1
Output: 1
```

**Exemple 3 :**
```
Input: n = 10, bad = 7
Output: 7
Explication:
Versions 1-6 sont bonnes, versions 7-10 sont mauvaises
```

## 🎯 Contraintes

- `1 <= bad <= n <= 2^31 - 1`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème de binary search où on cherche le premier élément qui satisfait une condition.

</details>

<details>
<summary>Indice 2</summary>

Si la version mid est mauvaise, la première mauvaise version est soit mid, soit avant mid.

</details>

<details>
<summary>Indice 3</summary>

Si la version mid est bonne, la première mauvaise version est forcément après mid.

</details>

<details>
<summary>Indice 4</summary>

Utilisez le template "find first" du binary search : quand vous trouvez une condition vraie, continuez à chercher à gauche.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

// API simulée - dans le vrai problème, cette fonction est fournie
var badVersion int

func isBadVersion(version int) bool {
    return version >= badVersion
}

func firstBadVersion(n int) int {
    // Votre code ici

}

func main() {
    // Test cases
    badVersion = 4
    fmt.Println(firstBadVersion(5))   // Expected: 4

    badVersion = 1
    fmt.Println(firstBadVersion(1))   // Expected: 1

    badVersion = 7
    fmt.Println(firstBadVersion(10))  // Expected: 7

    badVersion = 1
    fmt.Println(firstBadVersion(3))   // Expected: 1

    badVersion = 3
    fmt.Println(firstBadVersion(3))   // Expected: 3

    badVersion = 2
    fmt.Println(firstBadVersion(4))   // Expected: 2

    badVersion = 1702766719
    fmt.Println(firstBadVersion(2126753390))  // Expected: 1702766719

    badVersion = 1
    fmt.Println(firstBadVersion(2))   // Expected: 1
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func firstBadVersion(n int) int {
    left, right := 1, n

    for left < right {
        mid := left + (right-left)/2

        if isBadVersion(mid) {
            // mid est mauvaise, la première pourrait être mid ou avant
            right = mid
        } else {
            // mid est bonne, la première mauvaise est après mid
            left = mid + 1
        }
    }

    return left
}
```

**Explication détaillée :**

1. **Espace de recherche :** De 1 à n (toutes les versions)

2. **Condition de recherche :** Trouver la **première** version mauvaise
   - Template "find first" : `left < right` (pas `<=`)

3. **Logique de binary search :**
   - Si `isBadVersion(mid) == true` : mid est mauvaise, mais on cherche la **première**
     - La première pourrait être mid ou avant mid
     - `right = mid` (on garde mid dans l'espace de recherche)

   - Si `isBadVersion(mid) == false` : mid est bonne
     - La première mauvaise est forcément après mid
     - `left = mid + 1`

4. **Convergence :** Quand `left == right`, on a trouvé la première mauvaise version

**Exemple de trace pour `n = 5, bad = 4` :**

```
Début: left=1, right=5
mid=3, isBadVersion(3)=false → left=4
mid=4, isBadVersion(4)=true → right=4
left==right=4 → retourner 4
```

**Complexité :**
- Temps : O(log n)
- Espace : O(1)
- Appels API : O(log n)

**Points clés :**
- Template "find first" avec `left < right`
- Quand condition vraie, chercher à gauche (`right = mid`)
- Quand condition fausse, chercher à droite (`left = mid + 1`)
- Minimise les appels API grâce à la complexité logarithmique

</details>

## 🚀 Exercices similaires

- [Binary Search](binary-search.md) - Version de base
- [Search Insert Position](search-insert.md) - Même pattern de recherche
- [Find First and Last Position](../medium/find-range.md) - Extension avec "find first"

## 🎓 Concepts clés

- **Binary Search avec API** : utilisation d'une fonction externe
- **Template "Find First"** : chercher le premier élément qui satisfait une condition
- **Optimisation des appels** : réduire les interactions coûteuses
- **Condition de boucle** : `left < right` pour "find first"