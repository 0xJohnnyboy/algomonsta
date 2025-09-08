# 🧭 Cheatsheet - Guide de Sélection d'Algorithmes

## 🌳 Arbre de Décision - Quel Pattern Choisir ?

```mermaid
flowchart TD
    Start([Quel type de problème ?]) --> Array{Problème sur<br/>tableau/chaîne ?}
    
    Array -->|Oui| Sorted{Données triées<br/>ou recherche ?}
    Array -->|Non| Structure{Structure de<br/>données spéciale ?}
    
    Sorted -->|Recherche/Optimisation| BinSearch[🔍 Binary Search]
    Sorted -->|Paires d'éléments| TwoPtr[👥 Two Pointers]
    Sorted -->|Non trié| SubArray{Sous-tableau<br/>continu ?}
    
    SubArray -->|Fenêtre fixe/variable| SlidingWindow[🪟 Sliding Window]
    SubArray -->|Non continu| Other1{Voir autres<br/>patterns}
    
    Structure -->|Arbre| Tree{Type d'exploration<br/>arbre ?}
    Structure -->|Graphe| Graph{Type d'exploration<br/>graphe ?}
    Structure -->|Pile/File| Stack{Maintenir un<br/>ordre spécifique ?}
    
    Tree -->|Profondeur d'abord| DFS[🌲 DFS on Trees]
    Tree -->|Niveau par niveau| BFS_Tree[📊 BFS Level Order]
    
    Graph -->|Plus court chemin| BFS[🗺️ BFS on Graphs]
    Graph -->|Tous les chemins| DFS_Graph[🌐 DFS on Graphs]
    
    Stack -->|Oui| MonotonicStack[📚 Monotonic Stack]
    Stack -->|Non| Other2{Voir autres<br/>patterns}
    
    Other1 --> Exhaustive{Exploration<br/>exhaustive ?}
    Other2 --> Exhaustive
    
    Exhaustive -->|Toutes les solutions| Backtrack[🔄 Backtracking]
    Exhaustive -->|Solution optimale| Dynamic{Sous-problèmes<br/>qui se chevauchent ?}
    
    Dynamic -->|Oui| DP[💎 Dynamic Programming]
    Dynamic -->|Non| Greedy[🎯 Greedy/Autres]
    
    BinSearch --> BinEx[Exemples: Search in Rotated Array,<br/>Koko Eating Bananas, Find Peak]
    TwoPtr --> TwoEx[Exemples: Two Sum, Valid Palindrome,<br/>Remove Duplicates]
    SlidingWindow --> SlideEx[Exemples: Max Average Subarray,<br/>Longest Substring, Min Window]
    DFS --> DFSEx[Exemples: Max Depth, Path Sum,<br/>Validate BST]
    BFS --> BFSEx[Exemples: Word Ladder, Rotting Oranges,<br/>Number of Islands]
    MonotonicStack --> StackEx[Exemples: Daily Temperatures,<br/>Largest Rectangle, Next Greater]
    Backtrack --> BackEx[Exemples: Generate Parentheses,<br/>N-Queens, Word Search]
    DP --> DPEx[Exemples: Climbing Stairs, Coin Change,<br/>Longest Common Subsequence]
    
    style Start fill:#e1f5fe
    style BinSearch fill:#fff3e0
    style TwoPtr fill:#e8f5e8
    style SlidingWindow fill:#f3e5f5
    style DFS fill:#fff8e1
    style BFS fill:#e0f2f1
    style MonotonicStack fill:#fce4ec
    style Backtrack fill:#f1f8e9
    style DP fill:#e8eaf6
```

## 🎯 Guide de Questions pour Identifier le Pattern

### 📝 Questions à se poser dans l'ordre :

#### 1️⃣ **Type de données**
- [ ] **Tableau/Chaîne** → Continuer vers questions spécifiques
- [ ] **Arbre** → DFS ou BFS selon le besoin
- [ ] **Graphe** → BFS (plus court chemin) ou DFS (exploration)
- [ ] **Autres structures** → Analyser les opérations nécessaires

#### 2️⃣ **Si tableau/chaîne - Propriétés**
- [ ] **Données triées** → Two Pointers ou Binary Search
- [ ] **Recherche/Optimisation** → Binary Search
- [ ] **Paires d'éléments** → Two Pointers
- [ ] **Sous-tableau continu** → Sliding Window

#### 3️⃣ **Si exploration - Objectif**
- [ ] **Plus court chemin** → BFS
- [ ] **Tous les chemins** → DFS
- [ ] **Niveau par niveau** → BFS
- [ ] **Propriétés d'arbres** → DFS

#### 4️⃣ **Si optimisation - Structure**
- [ ] **Maintenir un ordre** → Monotonic Stack
- [ ] **Sous-problèmes répétés** → Dynamic Programming
- [ ] **Toutes les solutions** → Backtracking
- [ ] **Choix glouton** → Greedy

## 🔍 Matrice de Décision Rapide

| Condition | Pattern | Complexité | Cas d'usage |
|-----------|---------|------------|-------------|
| Tableau trié + paires | Two Pointers | O(n) | Two Sum, Valid Palindrome |
| Fenêtre/sous-tableau | Sliding Window | O(n) | Max Average, Longest Substring |
| Recherche dans trié | Binary Search | O(log n) | Search, Find Peak |
| Next Greater/Smaller | Monotonic Stack | O(n) | Daily Temperatures, Largest Rectangle |
| Parcours d'arbre | DFS on Trees | O(n) | Path Sum, Max Depth |
| Plus court chemin | BFS on Graphs | O(V+E) | Word Ladder, Rotting Oranges |
| Toutes les solutions | Backtracking | O(b^d) | N-Queens, Generate Parentheses |
| Optimisation | Dynamic Programming | O(n²) typique | Coin Change, LCS |

## 🎨 Reconnaissance par Mots-Clés

### 🔍 **Binary Search**
```
✅ "search", "find target"
✅ "sorted array", "rotated array"  
✅ "minimum/maximum such that"
✅ "guess and check"
```

### 👥 **Two Pointers**
```
✅ "two numbers", "pair of elements"
✅ "sorted array/string"
✅ "palindrome", "reverse"
✅ "remove duplicates"
```

### 🪟 **Sliding Window**
```
✅ "subarray", "substring"
✅ "window of size k"
✅ "maximum/minimum in window"
✅ "contiguous", "consecutive"
```

### 📚 **Monotonic Stack**
```
✅ "next greater", "next smaller"
✅ "largest rectangle"
✅ "daily temperatures"
✅ "stock span"
```

### 🌲 **DFS on Trees**
```
✅ "binary tree", "tree traversal"
✅ "path sum", "root to leaf"
✅ "maximum depth", "height"
✅ "validate BST"
```

### 🗺️ **BFS on Graphs**
```
✅ "shortest path", "minimum steps"
✅ "level by level"
✅ "grid", "matrix"
✅ "connected components"
```

### 🔄 **Backtracking**
```
✅ "all possible", "find all"
✅ "permutations", "combinations"
✅ "generate all", "enumerate"
✅ "n-queens", "sudoku"
```

### 💎 **Dynamic Programming**
```
✅ "optimal", "minimum/maximum"
✅ "number of ways"
✅ "longest", "shortest"
✅ "subsequence", "knapsack"
```

## ⚡ Quick Decision Tree (Version Compacte)

```mermaid
flowchart LR
    Problem([Problème]) --> Q1{Trié ?}
    Q1 -->|Oui| Q2{Recherche ?}
    Q1 -->|Non| Q3{Continu ?}
    
    Q2 -->|Oui| BS[Binary Search]
    Q2 -->|Non| TP[Two Pointers]
    
    Q3 -->|Oui| SW[Sliding Window]
    Q3 -->|Non| Q4{Arbre/Graphe ?}
    
    Q4 -->|Arbre| DFS[DFS Trees]
    Q4 -->|Graphe| BFS[BFS Graphs]
    Q4 -->|Non| Q5{Ordre ?}
    
    Q5 -->|Oui| MS[Monotonic Stack]
    Q5 -->|Non| Q6{Exhaustif ?}
    
    Q6 -->|Toutes solutions| BT[Backtracking]
    Q6 -->|Optimal| DP[Dynamic Programming]
```

## 🧪 Test Rapide - Identifiez le Pattern

### Problème A
> "Trouver deux nombres dans un tableau trié dont la somme égale target"
<details>
<summary>Réponse</summary>
**Two Pointers** - Tableau trié + paire d'éléments
</details>

### Problème B  
> "Longueur de la plus longue sous-chaîne sans caractères répétés"
<details>
<summary>Réponse</summary>
**Sliding Window** - Sous-chaîne + fenêtre variable
</details>

### Problème C
> "Combien de jours attendre pour une température plus chaude ?"
<details>
<summary>Réponse</summary>
**Monotonic Stack** - Next greater element
</details>

### Problème D
> "Nombre minimum de pièces pour faire un montant"
<details>
<summary>Réponse</summary>
**Dynamic Programming** - Optimisation avec sous-problèmes
</details>

### Problème E
> "Générer toutes les permutations d'un tableau"
<details>
<summary>Réponse</summary>
**Backtracking** - Toutes les solutions possibles
</details>

## 📋 Checklist de Validation

Avant de commencer à coder, vérifiez :

- [ ] **J'ai identifié le pattern** principal
- [ ] **Je connais la complexité** attendue  
- [ ] **J'ai choisi le bon template** 
- [ ] **Je comprends les cas limites**
- [ ] **Je peux expliquer pourquoi** ce pattern est optimal

## 🎯 Anti-Patterns - Quand NE PAS utiliser

| Pattern | ❌ N'utilisez PAS si... |
|---------|------------------------|
| Two Pointers | Données non triées, pas de relation entre éléments |
| Sliding Window | Sous-problèmes non continus, pas de fenêtre claire |
| Binary Search | Données non ordonnées, pas de propriété monotone |
| Monotonic Stack | Pas de comparaison avec éléments précédents/suivants |
| DFS Trees | Besoin du plus court chemin, exploration par niveau |
| BFS Graphs | Pas besoin du plus court chemin, mémoire limitée |
| Backtracking | Une seule solution suffit, pas d'exploration exhaustive |
| Dynamic Programming | Pas de sous-problèmes répétés, solution greedy existe |

---

**💡 Conseil :** Gardez cette cheatsheet ouverte pendant vos sessions de pratique. Avec le temps, la reconnaissance des patterns deviendra automatique !
