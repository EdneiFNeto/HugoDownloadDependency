---
title: Detecção de cartão
weight: 55
disableToc: true
---

## Detecção de cartão

- Faz-se uso da classe `SmartPOSCardDetectionRequestBuilder`.

### Métodos disponíveis

#### Cartão inserido

- Request para verificar que o cartão foi inserido.

##### Uma vez que este request é realizado, roda uma tarefa em segundo plano consultando o momento que o cartão é inserido, sendo interrompido de duas maneiras:
- Sendo cancelado explicitamente `request.cancel()`.
-  No momento que o cartão é inserido.

#### Exemplo

```Kotlin
    val request = SmartPOSPlugin.createCardDetectionRequestBuilder()
        .callback(object : Callback<SmartPOSCardDetectionResponse>() {
            override fun onSuccess(response: SmartPOSCardDetectionResponse) {
                if (response.cardWasDetected) {
                    handleCardWasDetected()
               }
            }

            override fun onFail(error: Throwable) {
            }

        })
        .build()
    Zoop.post(request!!)
```

#### Cartão removido

- Request para verificar, ao fim de uma transação, que o cartão está inserido, e mandar remover, caso necessário. Uma vez que o cartão não esteja presente, será lançado o callback `onComplete`.

 
##### Uma vez que este request é realizado, roda uma tarefa em segundo plano consultando o momento que o cartão é removido, sendo interrompido de duas maneiras:
- Sendo cancelado explicitamente `request.cancel()`.
- No momento que o cartão é removido.
\
__obs__.: Caso o cartão não esteja presente no momento do request, `onComplete` é retornado instantaneamente.

#### Exemplo

```Kotlin
    val request = SmartPOSPlugin.createCardDetectionRequestBuilder()
        .cardDetectionType(CardDetectionTypeRequestField.CardDetectionType.REMOVED)
        .callback(object : Callback<SmartPOSCardDetectionResponse>() {
            override fun onSuccess(response: SmartPOSCardDetectionResponse) {
                CoroutineScope(Dispatchers.Main).launch {
                    if (response.cardWasDetected) {
                       handleCardStillInserted()
                    }
                }
            }

            override fun onFail(error: Throwable) {
            }

            override fun onComplete() {
                handleCardIsNotInserted()
            }

        })
        .build()
    Zoop.post(request)
```


