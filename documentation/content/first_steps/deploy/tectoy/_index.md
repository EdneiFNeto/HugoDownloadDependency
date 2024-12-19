+++
title = "Tectoy/Pax"
date = 2023-02-14T09:13:36-03:00
weight = 5
chapter = true
+++

## POS de desenvolvimento/debug

{{% notice tip %}} Você pode testar o deploy usando o [código do aplicativo de exemplo]({{%relref "downloads/_index.md#Downloads" %}}) disponibilizado.

{{% /notice %}}

Para realizar um deploy na máquina para teste, é possível utilizar o quickRun do Android Studio, conforme abaixo: 
![PayDroidTool](images/instant_run.JPG?width=100%)

Também é possível instalar via adb, após o build, navegar até a pasta em que o app se encontra e utilizar o comando:
\
`adb install -t nome_do_app.apk`

Caso já tenha uma versão anterior instalada, é possível instalar com o comando:
\
 `adb install -t -r nome_do_app.apk`

## POS de produção
Caso seja necessário gerar uma release, é preciso configurar a assinatura de aplicativo disponibilizada pelo fabricante (no caso do A910/A910S, consultar à Pax). 

Além da assinatura da aplicação, é necessário enviar o pacote com assinatura anterior para mais uma etapa de assinatura na Pax (sem essa etapa, não é possível instalar a aplicação no smartPOS de produção).

{{% notice tip %}} Só é possível instalar uma aplicação com nível de segurança igual à instalada no dispositivo, conforme tabela abaixo:

| Versão Anterior       | Nova versão                                            | Status |
|-----------------------|--------------------------------------------------------|--------|
| Assinatura do android | Assinada pela Pax                                      | Falha  |
| Assinada pela Pax     | Assinada pela Pax                                      | Sucesso|
| Não assinada          | Não assinada                                           | Sucesso|

Caso seja necessário instalar versões de níveis de assinatura diferentes, é necessário desinstalar a versão anterior.
{{% /notice %}}

#### Build da aplicação
Para buildar o pacote, basta navegar pelo Android Studio até as tasks do gradle, e selecionar a opção assemble.
![Build](images/gradle_tasks.JPG?width=100%)


#### Instalar aplicação numa máquina de produção
Observando os passos anteriores de assinatura de versão, para deploy em máquina de produção, não é possível instalar via adb, sendo necessário o uso de ferramenta específica. 
Para acesso à ferramenta, é necessário entrar em contato com a Tectoy.

