---
title: Cancelamento
weight: 30
disableToc: true
---

## Cancelamento

- Faz-se uso da classe `SmartPOSVoidRequestBuilder`.

#### Exemplo de Cancelamento 

```kt
val voidRequest = SmartPOSPlugin.createVoidRequestBuilder()
    .callback(object: Callback<SmartPOSVoidResponse>() {
        override fun onStart() {
            startLoadingAnimation()
        }
    
        override fun onSuccess(response: SmartPOSVoidResponse) {
            handleSucessfullVoid(response)
        }
        
        override fun onFail(exception: Throwable) {
            handleVoidFailure(exception)
        }
        
        override fun onComplete() {
            stopLoadingAnimation()
        }
    })
    .voidTransactionCallback(object: Callback<VoidTransaction>() {
          override fun onSuccess(response: VoidTransaction) {
            assembleList(response)
        }
        
        override fun onFail(exception: Throwable) {
        }
        
    })
    .messageCallback(object: Callback<MessageCallbackRequestField.MessageData>() {
        override fun onSuccess(messageData: MessageCallbackRequestField.MessageData) {
            displayMessage()
        }
    
        override fun onFail(throwable: Throwable) {
        }
    })
    .menuSelectionCallback(object: Callback<SmartPOSMenuOptions>() {
        override fun onSuccess(response: SmartPOSMenuOptions) {
            assembleOptionsList(response.options.iterable)
        }

        override fun onFail(error: Throwable) {
        }
    })
    .build()
   
Zoop.post(voidRequest)
```

#### Callbacks

##### .callback
Responsável pelo fluxo do cancelamento, início, processamento, conclusão (sucesso/falha).
\
    `onStart` -> Esta callback é sinalizada quando o fluxo do cancelamento começa, podendo ser sinalizado pela aplicação o início do processamento.
    \
    `OnSuccess` -> Neste momento, a transação foi cancelada, e você recebe o objeto `SmartPOSVoidResponse`contendo um  `*TransactionData`, contendo todos os dados da transação. Para utilizar o request de impressão, será necessário passar este objeto para o [request de impressão]({{%relref "functions/print/_index.md#Impressão" %}})
    \
    `OnFail` -> Falha no cancelamento, é recebido um exception, podendo ser do tipo: 
    \
        - `ZoopPaymentException` -> Falha no fluxo do pagamento, neste caso, passamos a mensagem de erro, podendo ser acessada como `exception.message`
        \
        - `ZoopTimeoutException` -> Tempo excedido na operação
        \
        - `ZoopClosedConnectionException` -> Conexão interrompida
        \
        - `ZoopNetworkException` -> Falha de conexão
        \
    `OnComplete` -> Sinaliza o final do fluxo do cancelamento, tanto em casos de sucesso/falha.


Referência:

```kt
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
```


##### .messageCallback 
Responsável pelas mensagens no fluxo do cancelamento. ex: "Aproxime, insira ou passe o cartão"
\
    `OnSuccess` -> Mensagem a ser exibida pela aplicação para o usuário, acessada como  `response.message`
    \
    `OnFail` -> Não recebe nenhum dado, esta callback não é chamada.

##### .menuSelectionCallback

Responsável por sinalizar que será necessário a seleção manual de uma aplicação do cartão.  
O POS deverá exibir uma lista com as opções, com o respectivo título recebido para cada objeto, e, em até 30 segundos deverá ser selecionado, caso não o seja, a operação será cancelada.  
Para selecionar é necessário utilizar o objeto recebido, selecionando no formato: `response.options.select(MenuOptionsData)`  
    `OnSuccess` -> Lista de itens para serem exibidos pela aplicação para o usuário. `SmartPOSMenuOptions`  
    `OnFail` -> Não recebe nenhum dado, esta callback não é chamada.

Referência:

```kt
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

##### .voidTransactionCallback
Responsável pela seleção da transação a ser cancelada, a partir de uma lista (a lista é forncida neste callback)
\
    `OnSuccess` -> É recebido um objeto `UserSelection<VoidTransaction>` contendo um objeto `iterable` (acessada por `response.items`) a ser exibida pela aplicação para o usuário, a seleção de transação para cancelamento, é feita neste objeto. Cada item no objeto é uma transação.
    \
    `OnFail` -> Não recebe nenhum dado, esta callback não é chamada.

Referência:

```kt
public final data class VoidTransaction(
    val id: String,
    val amount: String,
    val option: Option,
    val cardBrand: CardBrand,
    val date: String,
    val time: String
)
```
##### Observações
- Deverá ser apresentada uma listagem de transações para o usuário. Neste ponto, uma transação deverá ser selecionada, daí, será cancelada e notificada na `messageCallback`. 
- A transação é selecionada utilizando o método: `response.select(VoidTransaction)` no objeto `UserSelection<VoidTransaction>`
- O tempo de seleção para transação de cancelamento é de 30 segundos, após, a operação será cancelada e notificada na `messageCallback`.


