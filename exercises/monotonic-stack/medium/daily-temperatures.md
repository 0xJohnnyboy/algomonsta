# Exercice - Daily Temperatures

**Niveau :** Medium  
**Pattern :** Monotonic Stack  
**Retour au cours :** [Monotonic Stack Pattern](../../courses/04-stack-monotonic.md)

## 📝 Énoncé

Étant donné un tableau d'entiers `temperatures` représentant les températures quotidiennes, retournez un tableau `answer` tel que `answer[i]` est le nombre de jours que vous devez attendre après le `i`-ème jour pour obtenir une température plus chaude.

S'il n'y a pas de jour futur pour lequel cela est possible, gardez `answer[i] == 0` à la place.

## 🔍 Exemples

**Exemple 1 :**
```
Input: temperatures = [73,74,75,71,69,72,76,73]
Output: [1,1,4,2,1,1,0,0]
Explication:
- Jour 0 (73°): Le jour 1 a 74° → attendre 1 jour
- Jour 1 (74°): Le jour 2 a 75° → attendre 1 jour  
- Jour 2 (75°): Le jour 6 a 76° → attendre 4 jours
- Jour 3 (71°): Le jour 5 a 72° → attendre 2 jours
- Jour 4 (69°): Le jour 5 a 72° → attendre 1 jour
- Jour 5 (72°): Le jour 6 a 76° → attendre 1 jour
- Jour 6 (76°): Pas de température plus chaude → 0
- Jour 7 (73°): Pas de température plus chaude → 0
```

**Exemple 2 :**
```
Input: temperatures = [30,40,50,60]
Output: [1,1,1,0]
```

**Exemple 3 :**
```
Input: temperatures = [30,60,90]  
Output: [1,1,0]
```

## 🎯 Contraintes

- `1 <= temperatures.length <= 10^5`
- `30 <= temperatures[i] <= 100`

## 💡 Indices

<details>
<summary>Indice 1</summary>

C'est un problème classique de "Next Greater Element". Pensez à utiliser une stack pour mémoriser les indices des jours en attente.

</details>

<details>
<summary>Indice 2</summary>

Utilisez une stack monotonic **décroissante**. Quand vous trouvez une température plus chaude, poppez tous les jours plus froids.

</details>

<details>
<summary>Indice 3</summary>

Stockez les **indices** dans la stack, pas les températures. Cela permet de calculer la différence de jours.

</details>

<details>
<summary>Indice 4</summary>

Pour chaque jour i, poppez de la stack tous les jours j où `temperatures[j] < temperatures[i]`, et pour chacun, `result[j] = i - j`.

</details>

## 🔨 Template de solution

```go
package main

import "fmt"

func dailyTemperatures(temperatures []int) []int {
    // Votre code ici
    
}

func main() {
    // Test cases
    fmt.Println(dailyTemperatures([]int{73, 74, 75, 71, 69, 72, 76, 73}))
    // Expected: [1, 1, 4, 2, 1, 1, 0, 0]
    
    fmt.Println(dailyTemperatures([]int{30, 40, 50, 60}))
    // Expected: [1, 1, 1, 0]
    
    fmt.Println(dailyTemperatures([]int{30, 60, 90}))
    // Expected: [1, 1, 0]
}
```

## ✅ Solution

<details>
<summary>Voir la solution</summary>

```go
func dailyTemperatures(temperatures []int) []int {
    n := len(temperatures)
    result := make([]int, n) // Initialisé à 0 par défaut
    stack := []int{}         // Stack d'indices
    
    for i := 0; i < n; i++ {
        // Pop tous les jours avec température plus froide
        for len(stack) > 0 && temperatures[stack[len(stack)-1]] < temperatures[i] {
            prevDay := stack[len(stack)-1]      // Jour en attente
            stack = stack[:len(stack)-1]        // Pop
            result[prevDay] = i - prevDay       // Jours à attendre
        }
        
        // Push le jour actuel
        stack = append(stack, i)
    }
    
    // Les jours restants dans la stack n'ont pas de jour plus chaud
    // result[j] reste 0 pour ces jours
    
    return result
}
```

**Simulation détaillée pour `[73,74,75,71,69,72,76,73]` :**

```
i=0, temp=73: stack=[] → push 0 → stack=[0]
i=1, temp=74: stack=[0], 73<74 → pop 0, result[0]=1-0=1 → stack=[1]  
i=2, temp=75: stack=[1], 74<75 → pop 1, result[1]=2-1=1 → stack=[2]
i=3, temp=71: stack=[2], 75>71 → push 3 → stack=[2,3]
i=4, temp=69: stack=[2,3], 71>69 → push 4 → stack=[2,3,4]
i=5, temp=72: stack=[2,3,4], 69<72 → pop 4, result[4]=5-4=1
              stack=[2,3], 71<72 → pop 3, result[3]=5-3=2
              stack=[2], 75>72 → push 5 → stack=[2,5]
i=6, temp=76: stack=[2,5], 72<76 → pop 5, result[5]=6-5=1
              stack=[2], 75<76 → pop 2, result[2]=6-2=4
              stack=[] → push 6 → stack=[6]
i=7, temp=73: stack=[6], 76>73 → push 7 → stack=[6,7]

Résultat final: [1,1,4,2,1,1,0,0]
```

**Explication de l'algorithme :**

1. **Stack monotonic décroissante** : maintient les indices des jours en attente d'une température plus chaude
2. **Condition de pop** : `temperatures[stack_top] < temperatures[i]`
3. **Calcul des jours** : `i - prevDay` donne le nombre de jours d'attente
4. **Gestion automatique des zeros** : les éléments qui restent dans la stack n'ont pas de jour plus chaud

**Complexité :**
- Temps : O(n) - chaque élément est pushé et poppé au maximum une fois
- Espace : O(n) - dans le pire cas (températures décroissantes), tous les indices sont dans la stack

**Pourquoi ça marche :**
- Quand on trouve une température plus chaude, elle "résout" tous les jours plus froids en attente
- La stack maintient l'ordre des jours non résolus
- Les températures dans la stack sont en ordre décroissant (d'où "monotonic decreasing")

</details>

## 🎯 Points clés à retenir

1. **Monotonic Stack Pattern** : problème classique de "Next Greater Element"
2. **Stack d'indices** : permet de calculer les distances
3. **Condition de pop** : température actuelle > température du top de la stack
4. **Complexité O(n)** : beaucoup plus efficace que la force brute O(n²)

## 🚀 Exercices similaires

- [Next Greater Element I](../easy/next-greater-i.md) - Version simplifiée
- [Next Greater Element II](../medium/next-greater-ii.md) - Avec tableau circulaire
- [Online Stock Span](../medium/stock-span.md) - Problème similaire avec les prix

## 🔍 Variations

**Version avec tableau circulaire :**
Si le tableau était circulaire, on parcourrait 2×n éléments :
```go
for i := 0; i < 2*n; i++ {
    actualIndex := i % n
    // même logique
}
```

**Version Previous Greater :**
Pour trouver le jour précédent avec température plus chaude :
```go
// Au moment du push, vérifier la stack
if len(stack) > 0 {
    result[i] = i - stack[len(stack)-1]
}
```
