---
title: 1.7.2
weight: -2
disableToc: true
---

## Pacote
Basta modificar a versão no gradle, conforme exemplo:

```Kotlin
implementation("br.zoop.pos.plugin:smartpos-gertec-gpos720:1.7.2") // Para a Gertec GPOS720
implementation("br.zoop.pos.plugin:smartpos-pax-a910:1.7.2")       // Para a Pax A910
```

## Release Notes
- Aceitar um parâmetro do tipo `JsonObject` para o campo _metadata_ do pagamento.
- Correção de congelamento do teclado de senha para a Gertec GPOS720.
