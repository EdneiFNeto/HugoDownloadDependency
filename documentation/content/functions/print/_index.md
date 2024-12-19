---
title: Impressão
weight: 35
disableToc: true
---

## Impressão

- Faz-se uso da classe `SmartPOSPrinterRequestBuilder`.
 
{{% notice warning %}} Este SDK não tem suporte para criação de recibo de pagamento customizado. Caso o integrador queira fazer uso da impressora nesse formato, deve-se utilizar a API do fabricante com suporte do mesmo.

{{% /notice %}}

### Observações

O plugin é capaz de imprimir recibos do tipo:

```Kotlin
    ReceiptType {
        ESTABLISHMENT, // Via estabelecimento
        CUSTOMER, // Via cliente
        REPRINT_ESTABLISHMENT, // Reimpressão de via do estabelecimento
        REPRINT_CUSTOMER, // Reimpressão de via do cliente
        DETAILED_REPORT, // Relatório detalhado¹
        CONSOLIDATED_REPORT, // Relatório consolidado¹
        SHIFT_REPORT // Relatório de fechamento de turno¹
    }

```

**¹** Para esses fluxos, nesse momento, é necessário receber um objeto pronto do respectivo tipo.

#### Exemplo de Impressão

```Kotlin
val request = SmartPOSPlugin.createPrintRequestBuilder()
                .printData(Printer.PrintData(transactionData = transactionData))
                .receiptType(ReceiptType.ESTABLISHMENT)
                .callback(object : Callback<SmartPOSPrinterResponse>() {
                    override fun onStart() {
                        handlePrintStarted()
                    }

                    override fun onSuccess(response: SmartPOSPrinterResponse) {
                        handlePrintSuccess()
                    }

                    override fun onFail(error: Throwable) {
                        handlePrinterError()
                    }

                    override fun onComplete() {
                        handlePrintFinished()
                    }

                }).build()
            Zoop.post(request)
```

##### Parâmetros de entrada

| Chave      | Tipo | Objetivo                                            | Exemplo |
|-----------------------|---|--------------------------------------------------------|--------|
| `printData` | Printer.PrintData | Objeto contendo os dados de impressão | Printer.PrintData(transactionData = transactionData) |
| `receiptType`     | ReceiptType | Tipo do recibo | ReceiptType.ESTABLISHMENT |

Referência
```Kotlin
// Tipo do objeto a ser passado, nos casos de compra/cancelamento, sempre é passado do tipo TransactionData, os outros devem ser ignorados.
// Os tipos relacionados a relatório, são utilizados com o respectivo ReceiptType.
    PrintData(
        val transactionData: TransactionData? = null,
        val consolidatedReportData: ConsolidatedReportData? = null,
        val detailedReportData: DetailedReportData? = null,
        val closedShiftsReportData: ClosedShiftsReportData? = null
    )

    ReceiptType {
        ESTABLISHMENT("VIA ESTABELECIMENTO"),
        CUSTOMER("VIA CLIENTE"),
        REPRINT_ESTABLISHMENT("REIMPRESSAO VIA ESTABELECIMENTO"),
        REPRINT_CUSTOMER("REIMPRESSAO VIA CLIENTE"),
        DETAILED_REPORT("RELATORIO CONSOLIDADO"),
        CONSOLIDATED_REPORT("RELATORIO CONSOLIDADO"),
        SHIFT_REPORT("FECHAMENTO DE TURNO")
    }   

// Venda / Cancelamento / Reimpressão

    data class TransactionData(
    val value: Int?,
    val paymentType: Int?,
    val installments: Int?,
    val status: String?,
    val brand: String?,
    val address: String?,
    val sellerName: String?,
    val acquiring: String?,
    val pan: String?,
    val autoCode: String?,
    val documentType: String?,
    val document: String?,
    val nsu: String?,
    val date: String?,
    val hour: String?,
    val cv: String?,
    val arqc: String?,
    val aid: String?,
    val idPix: String?,
    val sellerReceipt: String?,
    val customerReceipt: String?,
    val approvalMessage: String?,
    var transactionId: String? // Id da transação
    val receiptId: String? = null, // Id do recibo, caso aplicável (hoje, apenas transações Pix)
    val pixId: String? = null, // Id do Pix
    val cardFingerprint: String? = null // Token único do cartão.
)

// Relatório consolidado

data class ConsolidatedReportData(
    val issueDate: String?,
    val sellerName: String?,
    val documentType: String?,
    val document: String?,
    val serialNumber: String?,
    val fromDate: String?,
    val toDate: String?,
    val fromTime: String?,
    val toTime: String?,
    val transactionData: List<ConsolidatedReportTransactionData>?,
    val summary: ConsolidatedReportSummary?
)

data class ConsolidatedReportTransactionData(
    val brand: String?,
    val status: String?,
    val totalValue: Int?,
    val totalTransactions: Int?
)

data class ConsolidatedReportSummary(
    val totalApproved: Int?,
    val totalCanceled: Int?,
    val totalTransactionsApproved: Int?,
    val totalTransactionsCanceled: Int?
)

// Relatório detalhado

data class DetailedReportData(
    val issueDate: String?,
    val sellerName: String?,
    val documentType: String?,
    val document: String?,
    val serialNumber: String?,
    val fromDate: String?,
    val toDate: String?,
    val fromTime: String?,
    val toTime: String?,
    val totalApproved: Int?,
    val totalCanceled: Int?,
    val approvedTransactions: List<DetailedReportTransactionData>?,
    val canceledTransactions: List<DetailedReportTransactionData>?
)


data class DetailedReportTransactionData(
    val paymentType: Int?,
    val amount: Int?,
    val date: String?,
    val time: String?,
    val brand: String?
)

// Relatório de fechamento de turno

data class ClosedShiftsReportData(
    val issueDate: String?,
    val sellerName: String?,
    val documentType: String?,
    val document: String?,
    val serialNumber: String?,
    val fromDate: String?,
    val toDate: String?,
    val fromTime: String?,
    val toTime: String?,
    val transactionData: List<ClosedShiftsReportTransactionData>?,
    val summary: ClosedShiftsReportSummary
)

data class ClosedShiftsReportTransactionData(
    val status: String?,
    val brand: String?,
    val value: Int?,
    val installments: Int?,
    val date: String?,
    val time: String?,
    val paymentType: Int?,
    val sumAmount: Int?,
    val transactionsAmount: Int?
)

data class ClosedShiftsReportSummary(
    val totalApproved: Int?,
    val totalCanceled: Int?,
    val totalTransactionsApproved: Int?,
    val totalTransactionsCanceled: Int?
)
```

### Diretrizes para Identificação e Registro de Comprovantes de Transação

Com o intuito de identificar facilmente a origem dos comprovantes e organizar adequadamente os documentos, recomenda-se incluir dados que permitam rastrear e diferenciar cada comprovante. Aqui estão algumas sugestões que você pode considerar:

#### Número de Série

É um identificador único atribuído ao dispositivo. Esse número é utilizado pelos fabricantes para identificar cada unidade de forma única.

#### Número da Versão

É um número que especifica a versão pública do aplicativo. Normalmente, ele é definido em um atributo no arquivo `build.gradle(.kts)` do seu aplicativo Android.

{{% notice info %}}
O número da versão incluído no identificador do comprovante impresso pelo plugin é derivado da propriedade **`versionName`** do `PackageInfo` obtido para o pacote do aplicativo pelo `PackageManager` do SDK Android.
{{% /notice %}}

#### Identificador de Captura do Cartão

Se o comprovante se refere a uma transação com cartão, incluir um identificador do modo de captura das informações do cartão pode esclarecer como essa transação foi realizada.

- **C - Chip de contato:** Para transações realizadas com o chip de contato do cartão.
- **N - _Contactless_/NFC:** Para transações realizadas via tecnologia de aproximação (_contactless_) ou NFC (_Near Field Communication_).
- **T - Tarja magnética:** Para transações realizadas com a tarja magnética do cartão.
- **F - _Fallback_ (qualquer tipo):** Para transações realizadas por métodos de _fallback_, quando o modo padrão não está disponível.

##### Exemplo

Na imagem abaixo, apresentamos alguns exemplos de comprovantes de pagamentos e de cancelamentos, impressos pelo plugin, constando um identificador ao final do comprovante. O identificador segue o formato _\<IDENTIFICADOR DE CAPTURA>\<NÚMERO DE SÉRIE>\<NÚMERO DA VERSÃO>_.

![Exemplos de comprovantes com identificador.](images/print.png?width=600px)
