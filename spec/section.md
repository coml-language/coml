# Spécification Technique COML : Structures des Blocs et Sections

## 1. Définition Sémantique
En COML, la hiérarchie des données est régie exclusivement par l'analyse de l'indentation en début de ligne. Contrairement aux formats utilisant des délimiteurs explicites (comme les accolades `{}` ou les mots-clés `end`), COML utilise un système d'analyse contextuelle basé sur l'état de l'alignement horizontal.

* **Section Parente (Block) :** Une section déclarée sans aucun espace blanc initial. Elle clôt s'il y a lieu le bloc précédent et initialise un nouveau nœud racine dans l'Arbre Syntaxique Abstrait (AST).
* **Sous-section (Sub-section) :** Une section déclarée avec une indentation obligatoire (espaces ou tabulations). Elle est automatiquement rattachée au bloc parent actif le plus proche.

---

## 2. Formalisation Mathématique du Parsing

Soit $L$ une ligne du fichier COML, représentée comme une séquence de caractères. Nous définissons la fonction d'indentation $\lambda(L)$ qui calcule le nombre de caractères d'espacement initiaux (espaces $\backslash\text{u}\{0020\}$ ou tabulations $\backslash\text{u}\{0009\}$) précédant le premier caractère non blanc :

$$\lambda(L) = \min \{ i \in \mathbb{N} \mid L[i] \neq \text{'\textbackslash u\{0020\}'} \land L[i] \neq \text{'\textbackslash u\{0009\}'} \}$$

Soit $S$ l'ensemble des sections détectées dans le document. Chaque section $s \in S$ possède un niveau de profondeur sémantique noté $P(s) \in \{0, 1\}$. La relation de parenté entre une ligne contenant une section $s_n$ et la section précédente $s_{n-1}$ est déterminée de la manière suivante :

$$P(s_n) = 
\begin{cases} 
0 & \text{si } \lambda(L_n) = 0 \\
1 & \text{si } \lambda(L_n) > 0 \quad \text{relativement au bloc } s_i \text{ actif où } P(s_i) = 0
\end{cases}$$

---

## 3. Cycle de Vie de l'AST (Abstract Syntax Tree)

Lorsqu'une instance de sous-section est validée par la condition $\lambda(L) > 0$, le compilateur ne crée pas un nouveau scope global, mais effectue une opération d'ingestion sous le nœud de la section parente active.

### Exemple de correspondance de flux

```ini
[graphics]      # λ(L) = 0 -> Initialise Block: graphics
    [vulkan]    # λ(L) = 4 -> Sub-section sous "graphics"
    ['c']       # λ(L) = 4 -> Sub-section sous "graphics" (Single-Quoted)
[audio]         # λ(L) = 0 -> Clôture "graphics", Initialise Block: audio
```

Génère l'arbre syntaxique invariant suivant :

```text
COML Document
 ├── Block: graphics
 │    ├── Sub-section: vulkan
 │    └── Sub-section: c
 └── Block: audio
```
