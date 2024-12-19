---
title: 1.8.3
weight: -4
disableToc: true
---

## Pacote
Basta modificar a versão no gradle, conforme exemplo:

```Kotlin
implementation("br.zoop.pos.plugin:smartpos-gertec-gpos720:1.8.3") // Para a Gertec GPOS720
implementation("br.zoop.pos.plugin:smartpos-pax-a910:1.8.3")       // Para a Pax A910
```

## Release Notes
- Correção do fallback de chip para tarja.
- Adicionado o relatório de transações falhadas e canceladas.
- Correção da função de retorno de número de série de dispositivos Gertec.
