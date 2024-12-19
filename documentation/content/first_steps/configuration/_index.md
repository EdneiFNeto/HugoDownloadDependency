---
title: Configuração do projeto
weight: 15
disableToc: true
---

## Configuração Inicial

- O download de dependências é realizado via maven, conforme exemplo abaixo:

```kt
// No gradle do projeto, apontar:

allprojects {
    repositories {
        google()
        mavenCentral()
        mavenLocal()
        maven {
            url = uri("https://maven.pkg.github.com/getzoop/zoop-package-public")

            credentials {
                username = "MyCompanyGH"    // Seu usuário do GitHub.
                password = "github_pat_..." // Sua PAT do GitHub.
            }
        }
    }
}

// E adicionar como dependência de acordo com o modelo:
implementation("br.zoop.pos.plugin:smartpos-gertec-gpos720:1.8.4") // Para a Gertec GPOS720
implementation("br.zoop.pos.plugin:smartpos-pax-a910:1.8.4")       // Para a Pax A910/A910S
```

{{% notice info %}}
A GitHub _personal access token_ (PAT) usada deve possuir no mínimo permissão para __ler e obter pacotes/artefatos__ de repositórios públicos.
{{% /notice %}}

{{% notice tip %}}
Caso você tenha dúvidas a respeito da GitHub PAT, acesse a página de nosso [repositório público](https://github.com/getzoop/zoop-package-public). Já no `README`, apresentamos um [guia](https://github.com/getzoop/zoop-package-public#gerando-uma-github-pat) para orientá-lo sobre como gerar uma GitHub PAT e quais permissões conceder a ela.
{{% /notice %}}

### Dependências

- Adicionar ao `build.gradle` a nível de módulo:

```kt
android {
    packagingOptions {
        jniLibs {
            useLegacyPackaging = true
        }
    }
}

dependencies {
    implementation("org.jetbrains.kotlin:kotlin-reflect:1.9.0")
    implementation("org.jetbrains.kotlinx:kotlinx-datetime:0.3.0")
    implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.1")
    implementation("com.squareup.okhttp:okhttp:2.5.0")
    implementation("com.squareup.okhttp3:okhttp:4.10.0")
}
```

- Marcar no manifest -> `android:extractNativeLibs="true"`

### Permissões

- São necessárias as seguintes permissões:

```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PRIVILEGED_PHONE_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

```

- Após esse passo, você estará pronto para consumir os serviços do plugin, seguindo para [inicialização]({{%relref "functions/plug/_index.md#Inicializar" %}})
