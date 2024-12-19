---
title: Ativação
weight: 10
disableToc: true
---

## Ativação

- É necessário inicializar o SDK sem passar as credenciais, afim de realizar a requisição para recuperar as credenciais.
```Kotlin
Zoop.Initialize(context)
```
- Neste momento, é necessário injetar o plugin do smartPOS, conforme abaixo: 
```Kotlin
val smartPOSPlugin = Zoop.make<SmartPOSPlugin>()
Zoop.plug(smartPOSPlugin)
```
- Com o sdk inicializado, é __necessário__ fazer uma ativação pelo seu dashboard para recuperar as credenciais*, utilizando um token gerado com a requisição de ativação, conforme exemplo a seguir.
![dashboardActivation](images/dashboardActivation.JPG?width=100%)

{{% notice tip %}} Cada dispositivo só precisa fazer essa ativação uma única vez afim de criar o dispositivo na nossa base de dados, associando o dispositivo ao estabelecimento, após isso, é possível inicializar o plugin diretamente com as credenciais recebidas.

{{% /notice %}}

### Exemplo

```Kotlin
val activationRequestBuilder = ZoopFoundationPlugin.createDashboardActivationRequestBuilder()
            .tokenCallback(object : Callback<DashboardTokenResponse>() {
                override fun onSuccess(response: DashboardTokenResponse) {
                   // "Apresentar token ao usuário: ${response.token}"
                    displayActivationToken(response.token)
                }

                override fun onFail(error: Throwable) {
                    // "Falha ao requisitar token"
                    displayTokenGenerationError()
                }
            }
            .confirmCallback(object : Callback<DashboardConfirmationResponse>() {
                override fun onSuccess(response: DashboardConfirmationResponse) {
                     /**
                     * Nesse ponto, é necessário guardar as credenciais localmente em um banco de dados/shared preferences,
                     * para usar na próxima inicialização, passando como parâmetro na ativação
                     */
                    saveCredentials(response)
                    displayTokenActiveMessage()
                }

                override fun onFail(error: Throwable) {
                    /**
                    Caso o login seja cancelado, receberá a resposta aqui, com mensagem "request canceled"
                    loginRequest.cancel()
                     */
                    // "Apresentar erro na confirmação do token: ${error.message}"
                    when (error) {
                        is ZoopTimeoutException -> displayTokenExpiredMessage()
                        else -> displayGenericTokenActivationMessageError()
                    }
                }
            })
            .themeCallback(object: Callback<DashboardThemeResponse>() {
                override fun onSuccess(response: DashboardThemeResponse) {
                    /**
                     * Aqui você recebe o esquema de cores configurado para o seller no dashboard,
                     * e também sinaliza o sucesso no fluxo de ativação do terminal.
                     */
                    applyBackgroundTheme(response)
                }

                override fun onFail(error: Throwable) {
                    warnDownloadBackgroundThemeError()
                }
            })
            .build()
        Zoop.post(activationRequestBuilder)    
```
*__Observação__:
Você pode armazenar as credenciais localmente com a resposta do `confirmCallback`, fazendo isso, é possível informá-las na próxima inicialização sem precisar passar pela ativação via dashboard.

## Outras formas de ativação

#### Request HTTP do tipo GET

No lugar do `{{serialdopos}}`, adicionar o número de série do dispositivo (disponível na etiqueta na carcaça do dispositivo).
```
https://payments.api.zoop.ws/zec-login/generateToken?serialNumber={{serialdopos}} 
```

Após realizar este request, o retorno será: 
```JSON
{
    "token": "12345678"
}
```

Com a resposta deste request, basta realizar o pareamento via dashboard conforme descrito anteriormente.

#### Ativação via aplicativo de exemplo

Instalar o app disponível [aqui]({{%relref "downloads/_index.md#Downloads" %}})

![activationApp](images/activationApp.jpg?width=100%)

1. Clicar no botão "Iniciar ativação".
2. Parear o token disponível no dashboard.
3. Ao fim da ativação, o app exibirá as credenciais.

