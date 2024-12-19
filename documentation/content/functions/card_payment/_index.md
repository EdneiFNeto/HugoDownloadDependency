---
title: Pagamento via cartão
weight: 20
disableToc: true
---

## Venda

{{% notice tip %}} É recomendado que você faça uma [consulta de chave transacional]({{%relref "functions/check_zoop_key/_index.md#Consultar\schave" %}}) na __inicialização do plugin__, caso a chave não exista no terminal, este deverá ser enviado para o fabricante(Pax), para correção. A ausência da chave impede a transação e não há correção do lado da Zoop.
{{% /notice %}}

- Faz-se uso da classe `SmartPOSPaymentRequestBuilder`.

### Input de senha
- No input de senha, a PAX configura o teclado como embaralhado por padrão. Para ter o teclado exibido corretamente, é necessário adicionar ao arquivo config.xml (ou criar, caso não exista), dentro de `res/values/`, alguns parâmetros, conforme abaixo:

```XML
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <bool name="keyboard_random">false</bool>
    <bool name="show_input_box">false</bool>
</resources>
```

#### Exemplo de Pagamento

```Kotlin
val paymentRequest = SmartPOSPlugin.createPaymentRequestBuilder()
    .amount(1000)
    .option(Option.CREDIT)
    .installments(2)
    //.autoPrintEstablishmentReceipt(false) Para desabilitar a impressão automática.
    //.referenceId("") Identificador próprio (opcional)
    //.metadata(buildJsonObject {}) Metadados adicionais (opcional)
    .callback(object: Callback<SmartPOSPaymentResponse>() {
        override fun onStart() {
            startLoadingAnimation()
        }
    
        override fun onSuccess(response: SmartPOSPaymentResponse) {
            handleSucessfullPayment(response)
        }
        
        override fun onFail(exception: Throwable) {
            handlePaymentFailure(exception)
        }
        
        override fun onComplete() {
            stopLoadingAnimation()
        }
    })
    .messageCallback(object: Callback<MessageCallbackRequestField.MessageData>() {
        override fun onSuccess(messageData: MessageCallbackRequestField.MessageData) {
                displayUserMessage(messageData.message)
        }
        
        override fun onFail(exception: Throwable) {
            
        }
    })
    .pinCallback(object: Callback<PinCallbackRequestField.PinData>() {
        override fun onSuccess(pinData: PinCallbackRequestField.PinData) {
            val eventType = pinData.getType();
            when (eventType) {
                Terminal.PinEventHandler.EventType.Start -> creatViewToDisplayPasswordInput()
                Terminal.PinEventHandler.EventType.Finish -> finishPasswordInput()
                Terminal.PinEventHandler.EventType.Inserted -> handlePasswordCaracterInput()
                Terminal.PinEventHandler.EventType.Removed -> handlePasswordCaracterRemoved()
                else -> handlePasswordCaracterCleared()
            }
        }

        override fun onFail(exception: Throwable) {
        }
    })
    .menuSelectionCallback(object: Callback<SmartPOSMenuOptions>() {
        override fun onFail(error: Throwable) {
        }

        override fun onSuccess(response: SmartPOSMenuOptions) {
            assembleOptionsList(response.options.iterable)
        }

        override fun onFail(error: Throwable) {
        }
    })
    .userInputCallback(object : Callback<UserInput>() {
        override fun onSuccess(response: UserInput) {
            when (response.type) {
                UserInputType.CVV -> { requestCvv() }
            }
        }

        override fun onFail(error: Throwable) {
        }

    })
    .build()
   
Zoop.post(paymentRequest)
```

##### Parâmetros de entrada

| Chave | Tipo | Objetivo | Exemplo |
|:-:|:--|:--|:-:|
| `amount` | Long | Valor da transação (em centavos). | `2` (R$ 0,02) |
| `option` | Option | Opção do pagamento (do tipo `Option`). | `Option.CREDIT` |
| `installments` | Long | Quantidade de parcelas. | `2` |
| `autoPrintEstablishmentReceipt` | Boolean | Imprimir ou não a via do estabelecimento automaticamente. | `true` |
| `referenceId` | String | Identificador próprio gerado pelo parceiro (opcional, máx. 50 caracteres) | `"237ab31-g99c-4e25-9hjs-32u4d3gf7fh2"` |
| `metadata` | String (em JSON) ou JsonObject | Metadados personalizados fornecidos pelo parceiro (opcional, máx. 512 caracteres). | `"{\"parcelado\":false,\"tentativa\":1,\"vencimento\":\"2024-02-15\",\"versao\":\"1.23.4\"}"` |

#### Opções de pagamento
```Kotlin
Option {
    CREDIT, // Crédito à vista
    CREDIT_WITH_INSTALLMENTS, // Crédito parcelado
    DEBIT // Débito
}
```

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
    `OnSuccess` -> Neste momento, a transação foi aprovada, e você recebe o objeto do tipo SmartPOSPaymentResponse, contendo um *TransactionData, acessível por `response.transactionData`, contendo todos os dados da transação, caso a impressão automática de recibo esteja ativa, o recibo do estabelecimento será impresso. Para utilizar o request de impressão, será necessário passar este objeto para o [request de impressão]({{%relref "functions/print/_index.md#Impressão" %}})
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
        val approvalMessage: String?, // Mensagem de aprovação ex: APROVADA PELO EMISSOR
        val aidLabel: String?, // Label do cartão
        var transactionId: String?, // Id da transação
        val receiptId: String? = null, // Id do recibo, caso aplicável (hoje, apenas transações Pix)
        val pixId: String? = null, // Id do Pix
        val cardFingerprint: String? = null // Token único do cartão.
    )    
```
##### .messageCallback 
Responsável pelas mensagens no fluxo do pagamento. ex: "Aproxime, insira ou passe o cartão"
\
    `OnSuccess` -> Mensagem a ser exibida pela aplicação para o usuário, acessada como  `response.message`
    \
    `OnFail` -> Não recebe nenhum dado, esta callback não é chamada.
##### .pinCallback
Responsável pelo input de senha, caso este seja necessário.
\
    `OnSuccess` -> Esta callback é chamada passando o objeto do tipo `PinCallbackRequestField.PinData`, possui o parâmetro `type`, do tipo `Terminal.PinEventHandler.EventType`, acessível por `response.type`.
    \
    `OnFail` -> Não recebe nenhum dado, esta callback não é chamada.

Referência:
```Kotlin
    Terminal.PinEventHandler.EventType {
        Start, /* É necessário criar um view, em que o teclado de senha será exibido de forma automática. Você pode implementar sua tela de senha, exibindo os caracteres conforme recebido nos outros eventos deste tipo.*/
        Finish, // Neste momento, o fluxo de senha foi finalizado, e a aplicação pode sinalizar pro usuário que agora a transação será processada.
        Inserted, // Caractere inserido na senha.
        Removed, // Caractere removido da senha.
        Cleared // Campo de senha foi limpo, todos os caracteres removidos.
    }
```

##### .menuSelectionCallback
Responsável por sinalizar que será necessário a seleção manual de uma aplicação do cartão.
\
O POS deverá exibir uma lista com as opções, com o respectivo título recebido para cada objeto, e, em até 30 segundos deverá ser selecionado, caso não o seja, a operação será cancelada. 
\
Para selecionar é necessário utilizar o objeto recebido, selecionando no formato: `response.options.select(MenuOptionsData)`
\
    `OnSuccess` -> Lista de itens para serem exibidos pela aplicação para o usuário. `SmartPOSMenuOptions`
    \
    `OnFail` -> Não recebe nenhum dado, esta callback não é chamada.

Referência:
```Kotlin
SmartPOSMenuOptions(
    val options: List<MenuOptionsData> // Lista de itens, acessado como response.options.iterable, cada item dentro possui
    val title: String // Título do tipo de seleção requisitada. ex: "Opções do cartão"
    val defaultOption: Int // Opção padrão pré definida.
)

// O item options, possui uma lista de itens do tipo MenuOptionsData
MenuOptionsData(
    val index: Int // Valor a ser exibido como índice do item na lista.
    val itemName: String // Título do item do menu, ex: "Crédito"
)
```

##### .userInputCallback
Responsável por solicitar inputs da aplicação, como o CVV do cartão.
\
    `OnSuccess` -> Recebe o objeto do tipo `UserInput`, com o parâmetro `type`, do tipo `UserInputType`, acessível por `response.type`. Para adicionar o input:  `userInput.input(cvv)`, ou  para cancelar `userInput.cancel()`
    \
    `OnFail` -> Não recebe nenhum dado, esta callback não é chamada.

Referência:
```Kotlin

enum class UserInputType {
    CVV,
    PHONE_NUMBER,
    AMOUNT,
    INSTALLMENTS,
    LAST_FOUR_DIGITS
}
```