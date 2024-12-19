---
title: Consolidado
weight: 5
disableToc: true
---

## Relatório consolidado

- Request que recupera um relatório consolidado por tipo de pagamento e bandeira do cartão, em um intervalo de tempo pré definido.

{{% notice tip %}} Você pode imprimir a via passando o objeto retornado para o [request de impressão]({{%relref "functions/print/_index.md#Impressão" %}})

{{% /notice %}}

#### Parâmetros

- `receiptType` -> Tipo de relatório, no caso do consolidado: ReceiptType.CONSOLIDATED_REPORT
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
    .receiptType(ReceiptType.CONSOLIDATED_REPORT)
    .reportFilter(filter)
    .callback(object : Callback<ReportResponse>() {
        override fun onFail(error: Throwable) {
            // Erro processando relatório
        }

        override fun onSuccess(response: ReportResponse) {
            /* Relatório consolidado: */ val data = response.reportData.consolidatedReportData
        }

    }).build().run(Zoop::post)
```

#### Retorno


```Kotlin
data class ConsolidatedReportData(
    val issueDate: String?, // Data de emissão do relatório
    val sellerName: String?, // Nome do seller
    val documentType: String?, // Tipo de documento ("CPF" ou "CNPJ")
    val document: String?, // Documento
    val serialNumber: String?, // Número de série do terminal
    val fromDate: String?, // Data de início do relatório
    val toDate: String?, // Data final do relatório
    val fromTime: String?, // Hora inicial do relatório
    val toTime: String?, // Hora final do relatório
    val transactionData: List<ConsolidatedReportTransactionData>?, // Lista de dados de transação conforme descrito abaixo
    val summary: ConsolidatedReportSummary? // Resumo do relatório consolidado conforme descrito abaixo
)

data class ConsolidatedReportTransactionData(
    val brand: String?, // Bandeira do cartão
    val status: String?, // Status da transação ("approved" ou "canceled")
    val paymentType: Int?, // Inteiro, seguindo a enum class Option, descrito abaixo
    val totalValue: Int?, // Valor total de vendas dessa bandeira/tipo de pagamento/status
    val totalTransactions: Int? // Quantidade de transações dessa bandeira/tipo de pagamento/status
)

data class ConsolidatedReportSummary(
    val totalApproved: Int?, // Valor total aprovado
    val totalCanceled: Int?, // Valor total cancelado
    val totalTransactionsApproved: Int?, // Quantidade total de transações aprovadas
    val totalTransactionsCanceled: Int? // Quantidade total de transações canceladas
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