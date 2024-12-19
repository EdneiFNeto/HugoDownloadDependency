---
title: 1.8.4
weight: -5
disableToc: true
---

## Pacote
Basta modificar a versão no gradle, conforme exemplo:

```Kotlin
implementation("br.zoop.pos.plugin:smartpos-gertec-gpos720:1.8.4") // Para a Gertec GPOS720
implementation("br.zoop.pos.plugin:smartpos-pax-a910:1.8.4")       // Para a Pax A910
```

{{% notice warning %}}
Será necessário adicionar novas permissões no `AndroidManifest.xml` do aplicativo.
{{% /notice %}}

```Kotlin
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

## Release Notes
- Correção do fluxo de confirmação de transações
- Adiciona o envio de logs de erro e métricas caso seja habilitado pela aplicação na inicialização
- Valida versão mínima de FW para dispositivos Gertec
- Adiciona uma callback no pagamento, responsável por capturar inputs da aplicação
