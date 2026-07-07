<div align="center">
<img src="https://raw.githubusercontent.com/coml-language/coml/refs/heads/main/icons/coml-100.png">

*Clarus Obvious, Minimal Language.*
===================================


</div>

[COML]: https://coml-language.github.io/coml.io/

```yaml
[package]:
  name     = "aevum"
  version  = "0.1.0"
  authors  = ["jclermonttt"]
  features = ["clock", "timer", "sync"]
 
  # Une tabulation/indentation ici crée
  # automatiquement ['package.dependencies']
  [dependencies]:
     host: "127.0.0.1"
     port: 5432

  # Même logique pour les environnements.
  # qappartiennent à project
  [features]:
     staging { debug: true }
     production { debug: false }
```


## License

[MIT]("https://github.com/coml-language/coml/blob/main/MIT")