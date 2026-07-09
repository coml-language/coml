# Spécification Technique COML : Structures des Blocs et Sections

## 1. Définition Sémantique
En COML, la hiérarchie des données est régie exclusivement par l'analyse de l'indentation en début de ligne. Contrairement aux formats utilisant des délimiteurs explicites (comme les accolades `{}` ou les mots-clés `end`), COML utilise un système d'analyse contextuelle basé sur l'état de l'alignement horizontal.

* **Section Parente (Block) :** Une section déclarée sans aucun espace blanc initial. Elle clôt s'il y a lieu le bloc précédent et initialise un nouveau nœud racine dans l'Arbre Syntaxique Abstrait (AST).
* **Sous-section (Sub-section) :** Une section déclarée avec une indentation obligatoire (espaces ou tabulations). Elle est automatiquement rattachée au bloc parent actif le plus proche.

---

## 2. Formalisation Mathématique du Parsing

Soit $L$ une ligne du fichier COML, modélisée comme une séquence de caractères indexed de $0$ à $|L|-1$. Nous définissons l'ensemble des caractères d'espacement de tête valides par $W = \{\text{'\textbackslash u\{0020\}'}, \text{'\textbackslash u\{0009\}'}\}$.

La fonction d'indentation $\lambda(L)$ calcule le nombre exact de caractères blancs consécutifs au début de la ligne avant de rencontrer un caractère structurel ou textuel :

$$\lambda(L) = \begin{cases} 
|L| & \text{si } \forall i \in \{0, \dots, |L|-1\}, L[i] \in W \\
\min \{ i \in \mathbb{N} \mid L[i] \notin W \} & \text{sinon}
\end{cases}$$

Soit $S = (s_1, s_2, \dots, s_n)$ la séquence ordonnée des sections détectées chronologiquement dans le document. Chaque section $s_k$ possède une propriété de profondeur sémantique notée $P(s_k) \in \{0, 1\}$. 

La valeur de $P(s_k)$ pour la ligne courante $L_k$ abritant la section $s_k$ est déterminée par l'application stricte du prédicat suivant :

$$P(s_k) = 
\begin{cases} 
0 & \text{si } \lambda(L_k) = 0 \\
1 & \text{si } \lambda(L_k) > 0
\end{cases}$$

### Détermination de la Parenté dans l'AST

Pour toute sous-section $s_k$ dont la profondeur $P(s_k) = 1$, son nœud parent direct dans l'arbre de configuration, noté $\text{parent}(s_k)$, correspond structurellement à la section de niveau $0$ la plus proche en remontant le flux du document :

$$\text{parent}(s_k) = s_i \quad \text{où} \quad i = \max \{ j \in \{1, \dots, k-1\} \mid P(s_j) = 0 \}$$

Si l'ensemble d'index $\{ j \in \{1, \dots, k-1\} \mid P(s_j) = 0 \}$ est vide alors que $P(s_k) = 1$, le fichier est syntaxiquement invalide.

---

## 3. Cycle de Vie de l'AST (Abstract Syntax Tree)

Lorsqu'une instance de sous-section est validée par la condition $\lambda(L) > 0$, le compilateur effectue une opération d'ingestion sous le nœud de la section parente active déterminée par la fonction d'index maximum.

### Exemple de correspondance de flux

```ini
[graphics]      # λ(L) = 0 -> P(s_1) = 0 -> Initialise Block: graphics
    [vulkan]    # λ(L) = 4 -> P(s_2) = 1 -> Enfant de s_1 (graphics)
    ['c']       # λ(L) = 4 -> P(s_3) = 1 -> Enfant de s_1 (graphics)
[audio]         # λ(L) = 0 -> P(s_4) = 0 -> Initialise Block: audio
```

Génère l'arbre syntaxique invariant suivant :

```text
COML Document
 ├── Block: graphics
 │    ├── Sub-section: vulkan
 │    └── Sub-section: c
 └── Block: audio
```
