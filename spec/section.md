# Spécification Technique COML : Structures des Blocs et Sections

## 1. Définition Sémantique
En COML, la hiérarchie des données est régie exclusivement par l'analyse de l'indentation en début de ligne. Contrairement aux formats utilisant des délimiteurs explicites (comme les accolades `{}` ou les mots-clés `end`), COML utilise un système d'analyse contextuelle basé sur l'état de l'alignement horizontal.

* **Section Parente (Block) :** Une section déclarée sans aucun espace blanc initial. Elle clôt s'il y a lieu le bloc précédent et initialise un nouveau nœud racine dans l'Arbre Syntaxique Abstrait (AST).
* **Sous-section (Sub-section) :** Une section déclarée avec une indentation obligatoire (espaces ou tabulations). Elle est automatiquement rattachée au bloc parent actif le plus proche.
* **Paire Clé-Valeur (Key-Value) :** Une entité de configuration contenant une clé d'identification affectée à une valeur. Une paire clé-valeur est sémantiquement assignée à la section ou sous-section active, et son niveau d'indentation doit être supérieur ou égal à celui de sa section parente.

### Système de Types Pris en Charge
Le système de types de COML pour les valeurs comprend :
* **String :** Séquence de caractères UTF-8.
* **Integer :** Valeur numérique entière signée ou non signée en base 10.
* **Float :** Valeur numérique à virgule flottante conforme au standard IEEE 754.

---

## 2. Formalisation Mathématique du Parsing

Soit $L$ une ligne du fichier COML, modélisée comme une séquence de caractères indexée de $0$ à $|L|-1$. Nous définissons l'ensemble des caractères d'espacement de tête valides par $W = \{\text{'\textbackslash u\{0020\}'}, \text{'\textbackslash u\{0009\}'}\}$.

La fonction d'espacement $I(L)$ calcule le nombre exact de caractères blancs consécutifs au début de la ligne avant de rencontrer un caractère structurel ou textuel :

$$I(L) = \begin{cases} 
|L| & \text{si } \forall i \in \{0, \dots, |L|-1\}, L[i] \in W \\
\min \{ i \in \mathbb{N} \mid L[i] \notin W \} & \text{sinon}
\end{cases}$$

Soit $S = (s_1, s_2, \dots, s_n)$ la séquence ordonnée des sections détectées chronologiquement dans le document. Chaque section $s_k$ possède une propriété de profondeur sémantique notée $P(s_k) \in \{0, 1\}$. 

La valeur de $P(s_k)$ pour la ligne courante $L_k$ abritant la section $s_k$ est déterminé par l'application stricte du prédicat suivant :

$$P(s_k) = 
\begin{cases} 
0 & \text{si } I(L_k) = 0 \\
1 & \text{si } I(L_k) > 0
\end{cases}$$

### Détermination de la Parenté dans l'AST

Pour toute sous-section $s_k$ dont la profondeur $P(s_k) = 1$, son nœud parent direct dans l'arbre de configuration, noté $\text{parent}(s_k)$, correspond structurellement à la section de niveau $0$ la plus proche en remontant le flux du document :

$$\text{parent}(s_k) = s_i \quad \text{où} \quad i = \max \{ j \in \{1, \dots, k-1\} \mid P(s_j) = 0 \}$$

### Assignation des Paires Clé-Valeur

Soit $K_m$ une ligne de configuration contenant une paire clé-valeur, apparaissant chronologiquement après une section $s_k$ mais avant $s_{k+1}$. Pour que l'affectation soit syntaxiquement valide, l'indentation de la ligne $I(K_m)$ doit respecter la contrainte stricte d'alignement avec sa section d'appartenance $s_k$ :

$$\text{target}(K_m) = s_k \quad \iff \quad \begin{cases} 
I(K_m) \ge I(L_k) & \text{si } P(s_k) = 1 \\
I(K_m) \ge 0 & \text{si } P(s_k) = 0 
\end{cases}$$

Si $I(K_m) < I(L_k)$ alors que $P(s_k) = 1$, l'alignement brise la portée de la sous-section et provoque une erreur de parsing.

---

## 3. Cycle de Vie de l'AST (Abstract Syntax Tree)

Lorsqu'une paire clé-valeur est validée par les contraintes d'indentation, elle est ingérée comme un membre direct de l'objet sémantique (section ou sous-section) actif dans l'arbre avec son type inféré sous-jacent.

### Exemple de correspondance de flux

```ini
[graphics]          # I(L) = 0 -> P(s_1) = 0 -> Initialise Block: graphics
    backend = "gl"  # I(K) = 4 -> Assigné à s_1 (graphics) | Type: String
    [vulkan]        # I(L) = 4 -> P(s_2) = 1 -> Enfant de s_1 (graphics)
        max_fps = 144 # I(K) = 8 -> Assigné à s_2 (vulkan) | Type: Integer
        scale = 1.5   # I(K) = 8 -> Assigné à s_2 (vulkan) | Type: Float
[audio]             # I(L) = 0 -> P(s_3) = 0 -> Initialise Block: audio
```

Génère l'arbre syntaxique invariant suivant :

```text
COML Document
 ├── Block: graphics
 │    ├── Member: backend = "gl" (String)
 │    └── Sub-section: vulkan
 │         ├── Member: max_fps = 144 (Integer)
 │         └── Member: scale = 1.5 (Float)
 └── Block: audio
```
