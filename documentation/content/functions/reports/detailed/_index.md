---
title: Detalhado
weight: 10
disableToc: true
---

## Relatório detalhado

- Request que recupera um relatório detalhado com todas as transações, em um intervalo de tempo pré definido.
{{% notice tip %}} Você pode imprimir a via passando o objeto retornado para o [request de impressão]({{%relref "functions/print/_index.md#Impressão" %}})

{{% /notice %}}
##### Parâmetros

- `receiptType` -> Tipo de relatório, no caso do consolidado: ReceiptType.DETAILED_REPORT
- `reportFilter` -> Filtro do relatório, utilizando a data class ReportFilter

```Kotlin
data class ReportFilter(
    val threshold: ReportThreshold? = null,
    val fromDate: DateParameters? = null,
    val fromTime: TimeParameters = TimeParameters(0, 0),
    val toDate: DateParameters? = null,
    val toTime: TimeParameters = TimeParameters(23, 59)
)

enum class ReportThreshold {
    TODAY,
    YESTERDAY,
    DAY_BEFORE_YESTERDAY,
    CUSTOM
}

data class DateParameters(
    val day: Int,
    val month: Int,
    val year: Int
)

data class TimeParameters(
    val hour: Int,
    val minute: Int
)
```

#### Exemplo

```Kotlin
SmartPOSPlugin.createReportsRequestBuilder()
    .receiptType(ReceiptType.DETAILED_REPORT)
    .reportFilter(filter)
    .callback(object : Callback<ReportResponse>() {
        override fun onFail(error: Throwable) {
            onErrorProcessingReport()
        }

        override fun onSuccess(response: ReportResponse) {
            /* Relatório detalhado: */ val data = response.reportData.detailedReportData
        }

    }).build().run(Zoop::post)
```

#### Retorno

```Kotlin
data class DetailedReportData(
    val issueDate: String?, // Data de emissão do relatório
    val sellerName: String?, // Nome do seller
    val documentType: String?, // Tipo de documento ("CPF" ou "CNPJ")
    val document: String?, // Documento
    val serialNumber: String?, // Número de série do terminal
    val fromDate: String?, // Data de início do relatório
    val toDate: String?, // Data final do relatório
    val fromTime: String?, // Hora inicial do relatório
    val toTime: String?, // Hora final do relatório
    val totalApproved: Int?, // Valor total aprovado
    val totalCanceled: Int?, // Valor total cancelado
    val approvedTransactions: List<DetailedReportTransactionData>?, // Lista de transações aprovadas
    val canceledTransactions: List<DetailedReportTransactionData>? // Lista de transações canceladas
)


data class DetailedReportTransactionData(
    val paymentType: Int?, // Inteiro, seguindo a enum class Option, descrito abaixo
    val amount: Int?, // Valor da transação em centavos
    val date: String?, // Data da transação
    val time: String?, // Hora da transação
    val brand: String? // Bandeira do cartão
)

enum class Option(val code: Int) {
    CREDIT(0), // Crédito
    DEBIT(1), // Débito
    CREDIT_WITH_INSTALLMENTS(2), // Crédito parcelado
    VOUCHER(3), // Voucher
    PIX(4), // Pix
    UNKNOWN(-1) // Desconhecido
}
```