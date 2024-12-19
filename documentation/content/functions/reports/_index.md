---
title: Relatórios
weight: 60
disableToc: true
---

## Relatórios

- Faz-se uso da classe `ReportRequestBuilder`.

{{% notice info %}} Os dados de transações/relatórios duram no banco de dados por 90 dias.

{{% /notice %}}

#### Relatórios
* [Consolidado]()
* [Detalhado]()
* [Turnos]()

#### Filtros
Os relatórios consolidado e detalhado podem ser gerados utilizando um intervalo customizado, ou um padrão do tipo hoje, ontem ou anteontem.
\
Exemplos:
\
Para gerar um relatório de intervalo fixo:
```Kotlin
/* Hoje -> */ ReportFilter(threshold = ReportThreshold.TODAY)
/* Ontem -> */ ReportFilter(threshold = ReportThreshold.YESTERDAY)
/* Anteontem -> */ ReportFilter(threshold = ReportThreshold.DAY_BEFORE_YESTERDAY)
```
Para gerar um relatório customizado, é preciso passar os parâmetros de data e hora de início/fim, além do tipo, conforme abaixo:
```Kotlin
ReportFilter(
    threshold = ReportThreshold.CUSTOM,
    fromDate = DateParameters(18, 05, 2023),
    fromTime = TimeParameters(0, 0),
    toDate = DateParameters(20, 05, 2023),
    toTime = TimeParameters(23, 59)
)

data class DateParameters(
    val day: Int,
    val month: Int,
    val year: Int
) 

data class TimeParameters(
    val hour: Int,
    val minute: Int
)

// Este exemplo representa um intervalo do início do dia 18/05/2023 até o fim do dia 20/05/2023    
```


#### Retornos
Este request retorna uma data class chamada ReportData, que pode conter um relatório consolidado, detalhado, ou de turno.

```Kotlin
data class ReportData(
    val consolidatedReportData: ConsolidatedReportData? = null,
    val detailedReportData: DetailedReportData? = null,
    val closedShiftsReportData: ClosedShiftsReportData? = null
)
```
