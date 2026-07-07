<div align="center">
<img src="https://raw.githubusercontent.com/coml-language/coml/refs/heads/main/icons/coml-100.png">

*Clarus Obvious, Minimal Language.*
===================================


</div>

[COML]: https://coml-language.github.io/coml.io/

```toml
[package]:
   name: "aevum"
   authors = ["jclermonttt"]
   version: "0.1.0"
   features: ["clock", "timer", "sync"]

   # Une tabulation/indentation ici crée
   automatiquement 'project.database'
   [database]:
      host: "127.0.0.1"
      port: 5432

   # Même logique pour les environnements.
   qappartiennent à project
   [environments]:
      staging { debug: true }
      production { debug: false }
```


## License

[MIT]:"https://github.com/coml-language/coml/blob/main/MIT"