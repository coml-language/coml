<div align="center">
<img src="https://raw.githubusercontent.com/coml-language/coml/refs/heads/main/icons/coml-100.png">

*Clarus Obvious, Minimal Language.*
===================================


</div>

[COML]: https://coml-language.github.io/coml.io/

```
[project]:
   name: "aevum"
   version: "0.1.0"
   features: ["clock", "timer", "sync"]

   # Une tabulation/indentation ici crée
   automatiquement 'project.database'
   [database]:
      host: "127.0.0.1"
      port: 5432

   # Même logique pour les environnements qui       appartiennent à project
   [environments]:
      staging { debug: true }
      production { debug: false }

# Retour au niveau 0 = Nouvelle racine
indépendante de 'project'
[meta]:
   author: "Dev Team"

```
