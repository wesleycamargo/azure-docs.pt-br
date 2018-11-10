---
title: Sobre o SDK do serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos SDKs disponíveis para o serviço de fala.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: b946428f7d3962b2ac4b34fe6524c2079327f1c9
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218654"
---
# <a name="about-the-speech-service-sdk"></a>Sobre o SDK do serviço de fala

O SDK (Speech Service Software Development Kit) oferece aos aplicativos acesso nativo às funções do serviço de fala, facilitando o desenvolvimento de software. No momento, o SDK dá acesso a **Conversão de Fala em Texto**, **Tradução de Fala** e **Reconhecimento de Intenção**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obter o SDK

### <a name="windows"></a>Windows

Para o Windows, suportamos os seguintes idiomas:

* C# (UWP e .NET), C++: você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de Fala. O pacote inclui bibliotecas de clientes de 32 e 64 bits e bibliotecas gerenciadas (.NET). O SDK pode ser instalado no Visual Studio usando o NuGet. Pesquise **Microsoft.CognitiveServices.Speech**.

* Java: Você pode fazer referência e usar a versão mais recente do nosso pacote Speech SDK Maven, que suporta apenas o Windows x64. Em seu projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/`como um repositório adicional e faça referência`com.microsoft.cognitiveservices.speech:client-sdk:1.1.0` como uma dependência. 

### <a name="linux"></a>Linux

> [!NOTE]
> Atualmente, suportamos apenas o Ubuntu 16.04 em um PC (x86 ou x64 para desenvolvimento C ++ e x64 para .NET Core e Java).

Verifique se você tem o compilador e as bibliotecas necessários instalados executando os seguintes comandos do shell:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de Fala. Para referenciar o SDK, adicione a seguinte referência de pacote ao seu projeto:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.1.0" />
  ```

* Java: você pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de Fala. Em seu projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/`como um repositório adicional e faça referência`com.microsoft.cognitiveservices.speech:client-sdk:1.1.0` como uma dependência. 

* C++: Baixar o SDK como um [pacote. tar](https://aka.ms/csspeech/linuxbinary) e descompacte os arquivos em um diretório de sua escolha. A tabela a seguir mostra a estrutura da pasta do SDK:

  |Caminho|DESCRIÇÃO|
  |-|-|
  |`license.md`|Licença|
  |`ThirdPartyNotices.md`|Avisos de terceiros|
  |`include`|Arquivos de cabeçalho C e C++|
  |`lib/x64`|Biblioteca x64 nativa para vinculação ao seu aplicativo|
  |`lib/x86`|Biblioteca x86 nativa para vinculação ao seu aplicativo|

  Para criar um aplicativo, copie ou mova os binários necessários (e bibliotecas) para o seu ambiente de desenvolvimento. Incluí-las conforme necessário no processo de compilação.

### <a name="android"></a>Android

O SDK do Java para Android é empacotado como um [AAR (Biblioteca Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias, bem como as permissões necessárias do Android para usá-lo. Está hospedado em um repositório Maven em `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.1.0`.

Para consumir o pacote do seu projeto do Android Studio, faça as seguintes alterações:

* No arquivo build.gradle no nível do projeto, inclua o seguinte na seção `repository`:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* No arquivo build.gradle de nível de módulo, inclua o seguinte na seção `dependencies`:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.1.0'
  ```

O SDK do Java também faz parte do [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
