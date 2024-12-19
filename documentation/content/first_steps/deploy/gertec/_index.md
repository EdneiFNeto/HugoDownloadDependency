+++
title = "Gertec"
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
Caso seja necessário gerar uma release, é preciso configurar a assinatura de aplicativo disponibilizada pelo fabricante. 

#### Build da aplicação
Para buildar o pacote, basta navegar pelo Android Studio até as tasks do gradle, e selecionar a opção assemble.
![Build](images/gradle_tasks.JPG?width=100%)

#### Instalar aplicação numa máquina de produção
Para deploy em máquina de produção é necessário o uso de ferramenta específica. Para acesso à ferramenta, é necessário entrar em contato com a Gertec.