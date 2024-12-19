---
title: 1.5.0
weight: 35
disableToc: true
---

## Pacote
Basta modificar a versão no gradle, conforme exemplo:

```Kotlin
implementation("br.zoop.pos.plugin:smartpos:1.5.0-rc-1")
```

## Release Notes
- Corrige validação de comunicação com o sistema Zoop afim de notificar a mensagem correta em caso de indisponibilidade de sistema.
- Adiciona dois novos campos (`receiptId` e `pixId`) na data class `TransactionData`, passando a notificar o id correto do Pix no campo `transactionId`, e associando ao id anterior o campo `pixId`.
- Conexão com o sistema de pagamento do backend assíncrono, com ganho expressivo de performance até o momento de leitura de cartão.

{{% notice warning %}}
Com a adição dos campos `pixId` e `receiptId`, pode ser necessário adaptar o uso da classe `TransactionData` na sua aplicação.
{{% /notice %}}