---
title: Pagamento via Pix
weight: 25
disableToc: true
---

## Pix

- Faz-se uso da classe `SmartPOSPixPaymentRequestBuilder`.

#### Exemplo de Pix

```Kotlin
val pixRequest = SmartPOSPlugin.createPixPaymentRequestBuilder()
    .amount(1000)
    //.referenceId("") Identificador próprio (opcional)
    //.metadata(buildJsonObject {}) Metadados adicionais (opcional)
    .callback(object: Callback<SmartPOSPixPaymentResponse>(){
        override fun onSuccess(response: SmartPOSPixPaymentResponse) {
            handleSucessfullPayment(response)
        }

        override fun onFail(throwable: Throwable) {
            handlePaymentFailure(exception)
        }
    })
    .qrCodeCallback(object: Callback<QRCodeCallbackRequestField.QRCodeData>() {
        override fun onSuccess(qrCodeData: QRCodeCallbackRequestField.QRCodeData) {
            showQRCode(qrCodeData.getData());
        }

        override fun onFail(throwable: Throwable) {
            handleQrCodeFailure(throwable)
        }
    })
    .transactionIdCallback(object: Callback<TransactionIdCallbackRequestField.transactionIdData>() {
        override fun onSuccess(transactionId: TransactionIdCallbackRequestField.transactionIdData) {
            storeTransactionId(transactionId.data)
        }

        override fun onFail(throwable: Throwable) {
            handleTransactionIdFailure(throwable)
        }
    })
    .messageCallback(object: Callback<MessageCallbackRequestField.MessageData>() {
        override fun onSuccess(messageData: MessageCallbackRequestField.MessageData) {
            displayMessage()
        }

        override fun onFail(throwable: Throwable) {
        }
    })
    .build()
Zoop.post(request)
```

##### Parâmetros de entrada

| Chave | Tipo | Objetivo | Exemplo |
|:-:|:--|:--|:-:|
| `amount` | Long | Valor da transação (em centavos). | `2` (R$ 0,02) |
| `referenceId` | String | Identificador próprio gerado pelo parceiro (opcional, máx. 50 caracteres) | `"237ab31-g99c-4e25-9hjs-32u4d3gf7fh2"` |
| `metadata` | String (em JSON) ou JsonObject | Metadados personalizados fornecidos pelo parceiro (opcional, máx. 512 caracteres). | `"{\"parcelado\":false,\"tentativa\":1,\"vencimento\":\"2024-02-15\",\"versao\":\"1.23.4\"}"` |

#### Metadados em `JsonObject`

Para criar um objeto de `JsonObject`, faça como no seguinte exemplo:

```kt
val metadata = buildJsonObject {
    put("endereco","Avenida X")
    put("parcelado", false)
    put("tentativa", 1)
}
```

#### Callbacks

##### .callback
Responsável pelo fluxo do pagamento, início, processamento, conclusão (sucesso/falha).
\
    `onStart` -> Esta callback é sinalizada quando o fluxo do pagamento começa, podendo ser sinalizado pela aplicação o início do processamento.
    \
    `OnSuccess` -> Neste momento, a transação foi aprovada, e você recebe o objeto do tipo SmartPOSPixPaymentResponse, contendo um *TransactionData, acessível por `response.transactionData`, contendo todos os dados da transação,, para utilizar o request de impressão, será necessário passar este objeto para o [request de impressão]({{%relref "functions/print/_index.md#Impressão" %}}).
    \
    `OnFail` -> Falha na transação, é recebido um exception, podendo ser do tipo: 
    \
        - `ZoopPaymentException` -> Falha no fluxo do pagamento, neste caso, passamos a mensagem de erro, podendo ser acessada como `exception.message`
        \
        - `ZoopTimeoutException` -> Tempo excedido na operação
        \
        - `ZoopClosedConnectionException` -> Conexão interrompida
        \
        - `ZoopNetworkException` -> Falha de conexão
        \
    `OnComplete` -> Sinaliza o final do fluxo do pagamento, tanto em casos de sucesso/falha.

Referência:
```Kotlin
    TransactionData(
        val value: Int?, // Valor da transação em centavos
        val paymentType: Int?, // Tipo de pagamento
        val installments: Int?, // Parcelas
        val status: String?, // Status (approved/canceled)
        val brand: String?, // Marca do cartão ex: Visa
        val address: String?, // Endereço do seller
        val sellerName: String?, // Nome do seller
        val acquiring: String?, // Adquirente
        val pan: String?, // PAN do cartão
        val autoCode: String?, // Código de autorização
        val documentType: String?, // Tipo de documento, CPF/CNPJ
        val document: String?, // Documento
        val nsu: String?, // NSU
        val date: String?, // Data da transação
        val hour: String?, // Hora da transação
        val cv: String?, // CV
        val arqc: String?, // ARQC
        val aid: String?, // AID
        val sellerReceipt: String?, // Recibo do estabelecimento
        val customerReceipt: String?, // Recibo do cliente
        val approvalMessage: String?, // Mensagem de aprovação ex: APROVADA PELO EMISSOR,
        val aidLabel: String?, // Label do cartão
        var transactionId: String?, // Id da transação
        val receiptId: String? = null, // Id do recibo, caso aplicável (hoje, apenas transações Pix)
        val pixId: String? = null, // Id do Pix
        val cardFingerprint: String? = null // Token único do cartão.
    )    
    // No caso de uma transação Pix, ambos campos transactionId e pixId, sendo pixId um identificador interno da zoop.  
```

##### .qrCodeCallback
Responsável por receber o QRCode para exibição no POS.
\
    `OnSuccess` -> QRCode a ser exibida pela aplicação para o usuário, acessada como  `response.data`
    \
    `OnFail` -> Não recebe nenhum dado, esta callback não é chamada.

##### .transactionIdCallback
Responsável por receber o ID da transação.
\
    `onSuccess` -> ID da transação na infraestrutura Zoop, acessada em `response.data`.
    \
    `onFail` -> Não recebe nenhum dado, esta callback não é chamada.

##### .messageCallback
Responsável pelas mensagens no fluxo do pagamento. ex: "Aproxime, insira ou passe o cartão"
\
    `OnSuccess` -> Mensagem a ser exibida pela aplicação para o usuário, acessada como  `response.message`
    \
    `OnFail` -> Não recebe nenhum dado, esta callback não é chamada.

