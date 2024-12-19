---
title: Consultar chave transacional
weight: 40
disableToc: true
---

## Consultar chave

- Faz-se uso da classe `SmartPOSZoopKeyValidationRequestBuilder`.

### Observações

- Não é necessário ter inicializado o plugin previamente
- Caso o dispositivo não possua a chave, é necessário envio do terminal à fabricante(Pax) para gravação de chave.


#### Exemplo

```Kotlin
val request = SmartPOSPlugin.createZoopKeyValidationRequestBuilder()
    .callback(object: Callback<SmartPOSZoopKeyValidationResponse>() {
        override fun onSuccess(response: SmartPOSZoopKeyValidationResponse) {
            if (response.hasKey) {
            handleTerminalHasTransactionKey()
            } else {
            // terminal doesn't have transactional key
            }
        }

        override fun onFail(error: Throwable){
            // terminal doesn't have transactional key
        }
    })
    .build()
Zoop.post(request)
```