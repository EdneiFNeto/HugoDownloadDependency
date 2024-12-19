---
title: Token único por cartão
weight: 70
disableToc: true
---

## Token único por cartão

- Faz-se uso da classe `SmartPOSCardFingerprintRequestBuilder`.
- Este request retorna um identificador único do cartão criptografado (hash).

{{% notice tip %}} Esta requisição não tem aplicação prática para o fluxo transacional, sendo apenas uma ferramenta caso a sua aplicação faça alguma métrica baseada em cliente.
O mesmo dado é retornado ao fim de uma transação no objeto `TransactionData`

{{% /notice %}}

#### Exemplo

```Kotlin
    SmartPOSPlugin.createCardFingerprintRequestBuilder()
        .callback(object : Callback<SmartPOSCardFingerprintResponse>() {
            override fun onFail(error: Throwable) {
                // Falha ao solicitar o dado
            }

            override fun onSuccess(response: SmartPOSCardFingerprintResponse) {
                response.fingerprint // token
            }

        })
        .messageCallback(object : Callback<MessageCallbackRequestField.MessageData>() {
            override fun onFail(error: Throwable) {
                // Falha no fluxo de mensagens
            }

            override fun onSuccess(response: MessageCallbackRequestField.MessageData) {
                response.message // pode ser uma mensagem solicitando a inserção do cartão
            }

        })
        .build().run(Zoop::post)
```

#### Retorno

```Kotlin
/**
 * Classe responsável pela resposta de fingerprint do cartão.
 * @since v1.6.0
 * @param fingerprint Fingerprint do cartão.
 * @param cardBrand Bandeira do cartão (pode ser inconsistente caso não identificado corretamente no bin).
 */
data class SmartPOSCardFingerprintResponse(val fingerprint: String, val cardBrand: CardBrand? = null)
```