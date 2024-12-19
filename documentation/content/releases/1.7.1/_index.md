---
title: 1.7.1
weight: -1
disableToc: true
---

## Pacote
Basta incluir o plugin para o modelo, com a respectiva versão, no gradle, conforme exemplo:

```Kotlin
implementation("br.zoop.pos.plugin:smartpos-gertec-gpos720:1.7.1-rc-1") // Para a Gertec GPOS720
implementation("br.zoop.pos.plugin:smartpos-pax-a910:1.7.1-rc-1")       // Para a Pax A910
```

## Release Notes
- Adicionado suporte a Gertec GPOS720
- Adicionado suporte a metadados
- Obtenção do ID da transação para pagamentos por Pix
- Inclusão do número de parcelas para crédito parcelado no relatório detalhado
- Impressão do endereço na via do cliente
