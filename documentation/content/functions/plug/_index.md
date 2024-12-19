---
title: Inicialização
weight: 15
disableToc: true
---

## Inicialização

- Para utilizar o plugin, é necessário inicializar o SDK.

{{% notice tip %}} É necessário ativar o dispositivo conforme descrito [aqui.]({{%relref "functions/activation/_index.md#Ativação" %}})
{{% /notice %}}


##### Exemplo após ativação
```Kotlin
Zoop.Initialize(context) {
    credentials {
        marketplace = "{marketplace id}"
        seller = "{seller id}"
        accessKey = "{chave de acesso}"
    }
}
```
##### Dados de ativação
__marketplace:__ marketplace salvo durante a ativação via dashboard.  
__seller:__ seller salvo durante a ativação via dashboard.  
__accessKey:__ accessKey salvo durante a ativação via dashboard.

*** 

### Parâmetros extras

Na inicialização, é possível configurar os parâmetros listados abaixo.

#### Envio de logs de erro e métricas

Para habilitar o envio de logs e métricas para a Zoop, é necessário habilitar as flags

```Kotlin
Zoop.setSendErrorLogs(true) // O valor padrão é false
Zoop.setSendMetrics(true) // O valor padrão é false
```

#### Configurando o log

É possível configurar o nível de log e adicionar interceptors.

##### Configurando nível de log
```Kotlin
Zoop.setLogLevel(LogLevel.Debug)
```

##### Adicionando interceptor
```Kotlin
Zoop.addLogInterceptor { level, category, message ->
    MyLog(message)
}
```

##### Níveis de log disponíveis
```Kotlin
LogLevel.None
LogLevel.Information
LogLevel.Warning
LogLevel.Error
LogLevel.Critical
LogLevel.Log
LogLevel.Debug
LogLevel.Trace
LogLevel.Metric
```

#### Ativando Strict Mode

É possível ativar/desativar o strict mode. 

```Kotlin
Zoop.setStrict(true)
```

__Observação__:
Quando o SDK se encontra em strict mode, várias validações ocorrem quando um request está em andamento. Uma das verificações impede que o SDK atenda o request se o dispositivo estiver em modo desenvolvedor.
Então é sugerido utilizar `Zoop.setStric(false)` quando estiver em estágio de desenvolvimento. Para facilitar, pode ser utilizada a flag de debug `Zoop.setStrict(!Build.Debug)`.


#### Plugando

Após o SDK estar inicializado, conforme o passo anterior, é necessário criar a instância do plugin

##### Exemplo
```Kotlin
val smartPOSPlugin = SmartPOSPlugin(Zoop.constructorParameters())
Zoop.plug(smartPOSPlugin)
```
