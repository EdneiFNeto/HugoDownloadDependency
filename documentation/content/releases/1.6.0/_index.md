---
title: 1.6.0
weight: 25
disableToc: true
---

## Pacote
Basta modificar a versão no gradle, conforme exemplo:

```Kotlin
implementation("br.zoop.pos.plugin:smartpos:1.6.0-rc-1")
```

## Release Notes
- Adiciona consulta ao `cardFingerPrint` do cartão com um novo método descrito [aqui.]({{%relref "functions/card_fingerprint/_index.md#Token\súnico\spor\scartão" %}})
- O `cardFingerPrint` é um código único e seguro gerado a partir dos dados do cartão.
- Correção de um bug no relatório de fechamento de turno em que as transações eram agrupadas incorretamente.

{{% notice warning %}}
Com a adição do campo `cardFingerprint`, pode ser necessário adaptar o uso da classe `TransactionData` na sua aplicação.
{{% /notice %}}