---
title: Turnos
weight: 15
disableToc: true
---

## Relatório de turnos

- O relatório de turnos é constituído por 3 requisições:
    - Requisição que recupera um relatório do turno atual, ou, um turno previamente fechado.
    - Requisição para fechar o turno, que recebe uma estrutura recuperada no request anterior.
    - Requisição para recuperar uma lista com todos os turnos fechados anteriormente.

    
{{% notice info %}}
_Caso não exista um turno fechado, é listado todas as transações do dispositivo desde a primeira transação._
\
_Caso exista um turno previamente fechado, o turno subsequente é listado a partir da data/hora do último turno._

{{% /notice %}}

{{% notice tip %}} Você pode imprimir a via passando o objeto retornado para o [request de impressão]({{%relref "functions/print/_index.md#Impressão" %}})

{{% /notice %}}

### Requisição que recupera um relatório do turno atual, ou, um turno previamente fechado
#### Parâmetros
- `receiptType` -> Tipo de relatório, no caso de turno: ReceiptType.SHIFT_REPORT
- `shiftId` -> Id do turno a ser recuperado, ou nulo para turno atual.


#### Exemplo

```Kotlin
SmartPOSPlugin.createReportsRequestBuilder()
    .receiptType(ReceiptType.SHIFT_REPORT)
    .shiftId(null)
    .callback(object : Callback<ReportResponse>() {
        override fun onFail(error: Throwable) {
            // Erro processando relatório
        }

        override fun onSuccess(response: ReportResponse) {
            /* Relatório de turno: */ val data = response.reportData.closedShiftsReportData
        }

    }).build().run(Zoop::post)
```

#### Retorno


```Kotlin
data class ClosedShiftsReportData(
    val issueDate: String?, // Data de emissão do relatório
    val sellerName: String?, // Nome do seller
    val documentType: String?, // Tipo de documento ("CPF" ou "CNPJ")
    val document: String?, // Documento
    val serialNumber: String?, // Número de série do terminal
    val fromDate: String?, // Data de início do relatório
    val toDate: String?, // Data final do relatório
    val fromTime: String?, // Hora inicial do relatório
    val toTime: String?, // Hora final do relatório
    val transactionData: List<ClosedShiftsReportTransactionData>?, // Lista de transações do turno conforme descrito abaixo
    val summary: ClosedShiftsReportSummary, // Resumo do turno conforme descrito abaixo
    val transactions: List<TransactionData>? = null // Transações do turno
)

data class ClosedShiftsReportTransactionData(
    val status: String?, // Estado da transação ("approved" ou "canceled")
    val brand: String?, // Bandeira do cartão
    val value: Int?, // Valor em centavos
    val installments: Int?, // Número de parcelas
    val date: String?, // Data da transação
    val time: String?, // Hora da transação
    val paymentType: Int?, // Inteiro, seguindo a enum class Option, descrito abaixo
    val sumAmount: Int?, // Valor total de transações com este status
    val transactionsAmount: Int? // Quantidade de transações
)

data class ClosedShiftsReportSummary(
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

### Requisição para fechar o turno, que recebe uma estrutura recuperada no request anterior.

#### Parâmetros
- `closedShiftsReportData` -> Estrutura do tipo `ClosedShiftsReportData` recuperado utilizando a primeira requisição

#### Exemplo

```Kotlin
SmartPOSPlugin.createCloseShiftRequestBuilder()
    .closedShiftsReportData(shiftsReportData)
    .callback(object : Callback<Boolean>() {
        override fun onFail(error: Throwable) {
            // Falha ao fechar turno
        }

        override fun onSuccess(response: Boolean) {
            if (!response) {
                // Falha ao fechar turno
                return
            } else {
                // Turno fechado
            }
        }

    }).build().run(Zoop::post)
```


### Requisição para recuperar uma lista com todos os turnos fechados anteriormente.

#### Exemplo
```Kotlin
SmartPOSPlugin.createClosedShiftsRequestBuilder()
    .callback(object : Callback<ClosedShiftsResponse>() {
        override fun onFail(error: Throwable) {
            // Falha ao recuperar turnos fechados
        }

        override fun onSuccess(response: ClosedShiftsResponse) {
            if (response.shiftsList.isEmpty()) {
                // Sem turnos fechados
                return
            }
    
            /* List de turnos */ response.shiftsList        
        }

    })
    .build().run(Zoop::post)
```

#### Retorno
- `response.shiftsList` -> Tipo: `List<ReportContext.ClosedShifts>`

```Kotlin
data class ClosedShifts(
    val title: String, // Título do turno, no formato "data hora". ex: "18/05/2023 08:00"
    val index: Int // Índice do turno fechado, para ser utilizado na requição de recuperar um turno previamente fechado passando o parâmetro shiftId.
    )
```
