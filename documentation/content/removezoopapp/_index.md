+++
title = "Remove aplicativo GPOS720"
weight = 35
chapter = true
pre = "<b>7. </b>"
+++

### Capítulo 7
# Remoção do Aplicativo da Zoop da GPOS720
Este tutorial orienta como remover o aplicativo da Zoop utilizando um pendrive USB-C ou através de um MDM (Mobile Device Management).

#### APK removedor do APP na SmartPOS GPOS720
- [Link]({{< resource src="downloads/pendrive.zip" >}}) para download do arquivo .zip

#### Remoção via Pendrive USB-C

##### 1. Preparar o Pendrive
* Obtenha um Pendrive USB-C: Certifique-se de que o pendrive é do tipo USB-C, compatível com as portas das máquinas onde o aplicativo será removido.

##### 2. Limpar o Pendrive

* Conecte o pendrive ao seu computador. 
* Abra o explorador de arquivos e localize o pendrive. 
* Formate o pendrive, se necessário, para garantir que não haja arquivos indesejados.

##### 3. Descompactar o Conteúdo no Pendrive

* Descompacte o Arquivo Comprimido:
* Baixe o arquivo comprimido que contém o script ou programa de remoção do aplicativo. 
* Use um software de descompactação (como WinRAR ou 7-Zip) para extrair o conteúdo diretamente para o pendrive. 
* Verifique se todos os arquivos necessários estão presentes no pendrive após a extração:
  * `enable_launcher_v3.0-signed_ProductionF.apk`
  * `autoRun`

##### 4. Executar o Script de Remoção

* Conecte o Pendrive na Máquina de Destino:
* Insira o pendrive na porta USB-C da máquina onde o aplicativo será removido. 
    * Aguarde até que o script ou programa de remoção seja executado automaticamente.

- Confirme a Remoção:
  - Siga as instruções na tela para confirmar a remoção do aplicativo. 
  - Após a remoção, o sistema pode solicitar que o pendrive seja desconectado.

#### Demonstração de como ocorre a remoção via pendrive

{{< video src="videos/Demostracao_remover_appZoop_gpos720_pendrive.mp4" >}}

{{% notice info %}}
Caso o vídeo não seja exibido corretamente, você pode baixá-lo pelo [link]({{< resource src="videos/Demostracao_remover_appZoop_gpos720_pendrive.mp4" >}}).
{{% /notice %}}

#### Remoção via MDM

##### 1. Enviar o Executável de Remoção para as Máquinas

* Usar o MDM para Enviar o Executável:
  * Acesse o painel de controle do seu MDM. 
  * Envie o arquivo `enable_launcher_v3.0-signed_ProductionF.apk` ou outro executável de remoção fornecido para as máquinas de destino usando a ferramenta de distribuição de software do MDM.

##### 2. Executar o Script de Remoção

* Executar o Processo de Remoção:
  * Após o envio do executável para as máquinas, configure o MDM para iniciar a execução automaticamente ou instrua os usuários a realizarem a remoção manualmente.
  * Certifique-se de que o processo de remoção seja concluído em todas as máquinas designadas.

