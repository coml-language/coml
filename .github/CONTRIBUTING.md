# Contribuer à COML (Contributing Guidelines)

Merci de l'intérêt que vous portez au projet COML ! Afin de maintenir la clarté, la rigueur s'appliquant aux outils système, et de respecter la philosophie du projet, veuillez suivre ces directives avant de soumettre une contribution.

---

## 1. Philosophie du Design

Toute modification apportée à la syntaxe, au parseur ou à la structure de COML doit s'aligner sur nos trois piliers fondamentaux :
* **Clarus (Clarté) :** La configuration doit être immédiatement compréhensible par un humain sans ambiguïté visuelle.
* **Obvious (Évidence) :** Le comportement du code et l'arbre syntaxique abstrait (AST) généré doivent être une suite logique et prévisible de la mise en page.
* **Minimal (Minimalisme) :** Pas de fonctionnalités superflues. Si une configuration peut être exprimée avec les types actuels, nous n'ajoutons pas de complexité syntaxique inutile.

---

## 2. Processus de Développement & Architecture

### Exigences pour le Parseur et le Compilateur
* **Fondations du Lexer :** Le cœur du parseur réutilise et applique de manière stricte les spécifications de traitement des types de données (chaînes de caractères, valeurs numériques, structures en ligne et dates) issues des standards de configuration robustes de l'industrie. Toute analyse de valeur doit s'y conformer pour garantir un comportement déterministe.
* **Respect strict de l'alignement :** Le moteur de parsing superpose à ces types une validation basée sur la fonction d'espacement de tête $I(L)$. Tout changement touchant au traitement des espaces blancs (`\u{0020}`) ou des tabulations (`\u{0009}`) doit être validé mathématiquement par rapport aux spécifications formelles.
* **Validation des Identifiants :** Assurez-vous que l'exclusion du tiret (`-`) et de l'underscore (`_`) reste absolue pour les blocs parents ($P(s_k) = 0$).

### Workflow des Pull Requests
1. **Ouvrir une Issue :** Avant d'entamer des modifications structurelles majeures, ouvrez une issue pour documenter votre cas d'usage et obtenir la validation de l'architecture.
2. **Créer une Branche :** Utilisez une nomenclature explicite (ex: `feature/inline-table-optimization` ou `fix/lexer-whitespace-leak`).
3. **Tests Unitaires Obligatoires :** Toute correction ou nouvelle implémentation de type doit s'accompagner de tests de parsing (fichiers `.coml` valides et invalides confirmant la levée d'erreurs).
4. **Mise à jour de la documentation :** Si vous modifiez un comportement, assurez-vous d'impacter le `README.md` et le document de formalisation `SPEC.md`.

---

## 3. Normes de Code

* Le cœur du compilateur/parseur valorise la performance à bas niveau et la sécurité mémoire.
* Évitez les allocations dynamiques non nécessaires lors de la phase de lexing (privilégiez la manipulation de tranches de chaînes de caractères basées sur les indices d'octets de tête).
* Les messages d'erreurs retournés par le parseur lors d'une rupture d'alignement ou de la présence d'un caractère interdit (`Forbidden`) doivent indiquer précisément l'index de la ligne et le nombre de caractères blancs détectés.

---

## 4. Documentation Connexe

Avant de soumettre votre Pull Request, assurez-vous de maîtriser les spécifications formelles décrites dans les documents suivants :
* [Spécification Technique et Formelle COML](../spec/spec.md) — Pour les règles mathématiques d'ingestion de l'AST.
