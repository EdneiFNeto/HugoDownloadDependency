---
title: Visualizar último recibo
weight: 65
disableToc: true
---

## Visualizar último recibo

- Faz-se uso da classe `ReprintRequestBuilder`.
- Este request retorna a ultima transação realizada, com o objeto do tipo `TransactionData`.

{{% notice tip %}} Você pode imprimir a via passando o objeto retornado para o [request de impressão]({{%relref "functions/print/_index.md#Impressão" %}})

{{% /notice %}}

#### Exemplo

```Kotlin
SmartPOSPlugin.createLastReceiptRequestBuilder()
    .callback(object : Callback<LastReceiptResponse>() {
        override fun onFail(error: Throwable) {
            // Recibo não encontrado
        }

        override fun onSuccess(response: LastReceiptResponse) {
            val transactionData = response.transaction
        }

    }).build().run(Zoop::post)
```

#### Retorno

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
        val approvalMessage: String?, // Mensagem de aprovação ex: APROVADA PELO EMISSOR
        val aidLabel: String?, // Label do cartão
        var transactionId: String?, // Id da transação
        val receiptId: String? = null, // Id do recibo, caso aplicável (hoje, apenas transações Pix)
        val pixId: String? = null, // Id do Pix
        val cardFingerprint: String? = null // Token único do cartão.
    )
    // No caso de uma transação Pix, ambos campos transactionId e pixId, sendo pixId um identificador interno da zoop.  
```