# Spécification Technique COML : Structures des Blocs et Sections

## 1. Définition Sémantique
En COML, la hiérarchie des données est régie exclusivement par l'analyse de l'indentation en début de ligne. Contrairement aux formats utilisant des délimiteurs explicites (comme les accolades `{}` ou les mots-clés `end`), COML utilise un système d'analyse contextuelle basé sur l'état de l'alignement horizontal.

* **Section Parente (Block) :** Une section déclarée sans aucun espace blanc initial. Elle clôt s'il y a lieu le bloc précédent et initialise un nouveau nœud racine dans l'Arbre Syntaxique Abstrait (AST).
* **Sous-section (Sub-section) :** Une section déclarée avec une indentation obligatoire (espaces ou tabulations). Elle est automatiquement rattachée au bloc parent actif le plus proche.
* **Paire Clé-Valeur (Key-Value) :** Une entité de configuration contenant une clé d'identification affectée à une valeur, selon un formalisme analogue à TOML. Une paire clé-valeur est sémantiquement assignée à la section ou sous-section active, et son niveau d'indentation doit être supérieur ou égal à celui de sa section parente.

### Système de Types Pris en Charge
Le système de types de COML pour les valeurs comprend :
* **String :** Séquence de caractères UTF-8 délimitée.
* **Integer :** Valeur numérique entière signée ou non signée en base 10.
* **Float :** Valeur numérique à virgule flottante conforme au standard IEEE 754.
* **Inline Table :** Une collection de paires clé-valeur compacte et épurée définie sur une seule ligne entre accolades `{}`.
* **Date-Time :** Horodatage complet avec ou sans décalage horaire (conforme RFC 3339).
* **Date :** Une date calendrier pure (AAAA-MM-JJ).

---

## 2. Formalisation Mathématique du Parsing

```latex
Soit L une ligne du fichier COML, modélisée comme une séquence de caractères indexée de 0 à |L|-1.
Nous définissons l'ensemble des caractères d'espacement de tête valides par :
W = {'\u{0020}', '\u{0009}'}

La fonction d'espacement I(L) calcule le nombre exact de caractères blancs consécutifs au début de la ligne :
I(L) = |L|                              si ∀i ∈ {0, ..., |L|-1}, L[i] ∈ W
     = min { i ∈ N | L[i] ∉ W }        sinon

Soit S = (s_1, s_2, ..., s_n) la séquence ordonnée des sections détectées chronologiquement.
Chaque section s_k possède une propriété de profondeur sémantique notée P(s_k) ∈ {0, 1}.
La valeur de P(s_k) pour la ligne courante L_k abritant la section s_k est déterminée par :
P(s_k) = 0      si I(L_k) == 0
       = 1      si I(L_k) > 0
```

### Détermination de la Parenté dans l'AST

```latex
Pour toute sous-section s_k dont la profondeur P(s_k) == 1, son nœud parent direct dans l'arbre
de configuration, noté parent(s_k), correspond structurellement à la section de niveau 0
la plus proche en remontant le flux du document :

parent(s_k) = s_i   où   i = max { j ∈ {1, ..., k-1} | P(s_j) == 0 }
```

### Assignation des Paires Clé-Valeur

```latex
Soit K_m une ligne de configuration contenant une paire clé-valeur, apparaissant chronologiquement
après une section s_k mais avant s_{k+1}. Pour que l'affectation soit syntaxiquement valide,
l'indentation de la ligne I(K_m) doit respecter la contrainte stricte d'alignement avec sa section s_k :

target(K_m) = s_k   <=>   I(K_m) >= I(L_k)   si P(s_k) == 1
                          I(K_m) >= 0        si P(s_k) == 0

Si I(K_m) < I(L_k) alors que P(s_k) == 1, l'alignement brise la portée et provoque une erreur de parsing.
```

---

## 3. Cycle de Vie de l'AST (Abstract Syntax Tree)

Lorsqu'une paire clé-valeur est validée par les contraintes d'indentation, elle est ingérée comme un membre direct de l'objet sémantique (section ou sous-section) actif dans l'arbre avec son type inféré sous-jacent.

### Exemple de correspondance de flux

```ini
[graphics]          # I(L) = 0 -> P(s_1) = 0 -> Initialise Block: graphics
    meta = { api = "vulkan", level = 2 } # Inline Table
    [vulkan]        # I(L) = 4 -> P(s_2) = 1 -> Enfant de s_1 (graphics)
        max_fps = 144                    # Integer
        scale = 1.5                      # Float
        updated_at = 2026-07-09T13:38:00 # Date-Time
        release = 2026-07-09             # Date
[audio]             # I(L) = 0 -> P(s_3) = 0 -> Initialise Block: audio
```

Génère l'arbre syntaxique invariant suivant :

```text
COML Document
 ├── Block: graphics
 │    ├── Member: meta = { api = "vulkan", level = 2 } (Inline Table)
 │    └── Sub-section: vulkan
 │         ├── Member: max_fps = 144 (Integer)
 │         ├── Member: scale = 1.5 (Float)
 │         ├── Member: updated_at = 2026-07-09T13:38:00 (Date-Time)
 │         └── Member: release = 2026-07-09 (Date)
 └── Block: audio
```
