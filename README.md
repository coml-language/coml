<div align="center">
<img src="https://raw.githubusercontent.com/coml-language/coml/refs/heads/main/icons/coml-100.png">

*Clarus Obvious, Minimal Language.*
===================================
</div>

[COML]: https://coml-language.github.io/coml.io/

## Fonctionnalités Clés

* **Structure Guidée par l'Indentation :** L'absence ou la présence d'espaces blancs en début de ligne détermine nativement les blocs parents et leurs sous-sections.
* **Identifiants Clean & Contraints :** Protection stricte des blocs racines pour empêcher les structures confuses (les tirets et underscores sont exclus des blocs parents).
* **Système de Types Robuste :** Support natif des chaînes de caractères, entiers, flottants, tables en ligne, ainsi que des dates et horodatages précis.
* **Zéro Clé Entre Guillemets :** Pas de fioritures, pas d'échappements complexes dans les clés; la syntaxe n'autorise que des identifiants bruts (*bare*) ou enveloppés de quotes simples (*single-quoted*).

---

## Aperçu de la Syntaxe

Voici un exemple valide de fichier `.coml` :

```coml
[graphics]
    meta = { api = "vulkan", tier = 3 }
    
    [vulkan-core]
        max_fps = 144
        scale = 1.5
        updated_at = 2026-07-09T13:38:00Z

[audio]
    enabled = true
```

## License

Ce projet est distribué sous les termes de la Licence [MIT](LICENSE)
