---
title: 1.8.2
weight: -3
disableToc: true
---

# Nota Importante
Devido a alterações internas, houve uma mudança no processo de inicialização do plugin, a partir desta versão
a inicialização do plugin não será mais através da função Zoop.Make. Será feita através da nova função Zoop.constructorParameters()

- Exemplo de inicialização do plugin em versões anteriores à esta:
```Kotlin
Zoop.findPlugin<SmartPOSPlugin>() ?: Zoop.make<SmartPOSPlugin>().run(Zoop::plug)
```

- Exemplo de inicialização do plugin a partir desta versão:
```Kotlin
Zoop.findPlugin<SmartPOSPlugin>() ?: Zoop.plug(SmartPOSPlugin(Zoop.constructorParameters()))
```

## Dependências
Nesta nova versão, é necessário adcionar duas dependências a nível de aplicação.

```Kotlin
implementation("org.jetbrains.kotlinx:kotlinx-datetime:<version>")
implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:<version>")
```

Caso o dispositivo esteja usando uma versão abaixo do Android 8.0 (API level 26), deve ser adicionado a dependência _core library desugaring_.
Deve-se também neste caso utilizar uma versão do Android Gradle Plugin acima da 4.0

Nas dependências do projeto à nível de aplicação, implemente: 
```Kotlin
coreLibraryDesugaring("com.android.tools:desugar_jdk_libs:1.1.5")
```

Em compileOptions, à nível de aplicação, habilite a biblioteca:
```Kotlin
    compileOptions {
    isCoreLibraryDesugaringEnabled = true
}
```

## Pacote

Basta modificar a versão no gradle, conforme exemplo:

```Kotlin
implementation("br.zoop.pos.plugin:smartpos-gertec-gpos720:1.8.2") // Para a Gertec GPOS720
implementation("br.zoop.pos.plugin:smartpos-pax-a910:1.8.2")       // Para a Pax A910/A910S
```

## Release Notes

- Adição de suporte ao dispositivo Pax A910S;
- Adição de suporte a pagamento por cartão com tarja magnética (_mag stripe_);
- Adição de suporte ao campo _reference ID_ no pagamento por Pix;
- Adição de suporte a metadados no pagamento por Pix;
- Atualização do SDK dos dispositivos Pax;
- Correção de congelamento durante a inicialização para dispositivos Gertec;
- Correção da leitura de cartões com um PAN de 17 ou mais dígitos em dispositivos Gertec;
- Correção de comportamento ao capturar um CVV vazio;
- Correção de _timers_ durante a captura de dados do operador/usuário;
- Correção da impressão da mensagem de aprovação do pagamento por cartão quando esta se dá por múltiplos fatores.
