+++
title = "Métodos do fabricante"
date = 2023-02-14T09:13:36-03:00
weight = 30
chapter = true
pre = "<b>6. </b>"
+++

### Capítulo 6
# Métodos do fabricante

É possível utilizar um request para obter uma referência que dá acesso aos métodos do fabricante, possibilitando:

- Faz-se uso da classe `SmartPOSPublicDeviceRequestBuilder`.

### Métodos disponíveis

- `setMenuPasswordEnabled` -> Habilita ou desabilita a senha para acesso às configurações do Android.
- `setApn` -> Configura e conecta em uma apn, passando um objeto do tipo ApnCredentials.
- `setNavigationKeyEnabled` -> Habilita ou desabilita um botão físico de navegação do Android.
- `setAutoTimeZone` -> Habilita ou desabilita o fuso horário automático.
- `setAutoTime` -> Habilita ou desabilita a hora automática.
- `setTimeZone` -> Configura o fuso horário no formato Continent/Cidade ex: America/Sao_Paulo
- `getSerialNumber` -> Retorna o número de série do terminal.
- `isDebugDevice` -> Flag que indica se o terminal é de desenvolvimento.


#### Exemplo

```Kotlin
SmartPOSPlugin.createPublicDeviceRequestBuilder()
    .callback(object : Callback<WeakReference<PublicDevice>>() {
        override fun onFail(error: Throwable) {
        }

        override fun onSuccess(response: WeakReference<PublicDevice>) {
            response.get()?.setMenuPasswordEnabled(false)
        }

    }).build().run { Zoop.post(this) }
```
