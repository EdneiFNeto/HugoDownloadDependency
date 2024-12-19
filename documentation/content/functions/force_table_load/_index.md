---
title: Atualizar carga de tabelas
weight: 45
disableToc: true
---

## Forçar carga de tabelas

- Faz-se uso da classe `SmartPOSTableLoadRequestBuilder`.

### Observações

- Esta funcionalidade não é mandatória, visto que durante a transação, caso seja necessário, o dispositivo realizará a carga de tabelas, esta funcionalidade é apenas para forçar uma carga, sem prosseguir um fluxo de venda.

#### Exemplo

```Kotlin
val request = SmartPOSPlugin.createTableLoadRequestBuilder()
                .callback(object : Callback<SmartPOSTableLoadResponse>() {
                    override fun onFail(error: Throwable) {
                        handleFailTableLoad(error)
                    }

                    override fun onSuccess(response: SmartPOSTableLoadResponse) {
                        handleSuccessTableLoad(response)
                    }
                })
                .messageCallback(object : Callback<MessageCallbackRequestField.MessageData>() {
                    override fun onFail(error: Throwable) {
                    }

                    override fun onSuccess(response: MessageCallbackRequestField.MessageData) {
                        displayUserMessage(response.message)
                    }
                }).build()
            Zoop.post(request)
```