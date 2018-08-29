---
title: Sobre o SDK de Fala de Serviços Cognitivos
description: Uma visão geral dos SDKs disponíveis para o serviço de Fala.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 08/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 6b5796bf4d049579dbdede2251f2ca67cc9c4bfd
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "41929728"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Sobre o SDK de Fala de Serviços Cognitivos

O SDK (Software Development Kit) de Fala de Serviços Cognitivos fornece aos seus aplicativos acesso nativo às funções do serviço de Fala, tornando mais fácil desenvolver software. No momento, o SDK dá acesso a **Conversão de Fala em Texto**, **Tradução de Fala** e **Reconhecimento de Intenção**.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obter o SDK

### <a name="windows"></a>Windows

Para Windows, oferecemos suporte para as seguintes linguagens:

* C# (UWP e .NET), C++: você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de Fala.
  O pacote inclui bibliotecas de cliente de 32 bits e 64 bits, bem como bibliotecas gerenciadas (.NET).
  O SDK pode ser instalado no Visual Studio usando NuGet; simplesmente pesquise `Microsoft.CognitiveServices.Speech`.

* Java: você pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de Fala, que dá suporte ao Windows x64 apenas.
  Em seu projeto do Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e referencie `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` como uma dependência. 

### <a name="linux"></a>Linux

> [!NOTE]
> Atualmente, só há suporte para Ubuntu 16.04 em um PC (x86 ou x64 para desenvolvimento em C++, x64 para .NET Core e Java).

Verifique se você tem o compilador e as bibliotecas necessários instalados executando os seguintes comandos do shell:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de Fala.
  Para referenciar o SDK, adicione a seguinte referência de pacote ao seu projeto:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="0.6.0" />
  ```

* Java: você pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de Fala.
  Em seu projeto do Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/` como um repositório adicional e referencie `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` como uma dependência. 

* C++: baixe o SDK como um [pacote .tar](https://aka.ms/csspeech/linuxbinary) e desempacote os arquivos em um diretório de sua escolha. A tabela a seguir mostra a estrutura de pastas do SDK.

  |Caminho|DESCRIÇÃO|
  |-|-|
  |`license.md`|Licença|
  |`ThirdPartyNotices.md`|Avisos de terceiros|
  |`include`|Arquivos de cabeçalho C e C++|
  |`lib/x64`|Biblioteca x64 nativa para vinculação ao seu aplicativo|
  |`lib/x86`|Biblioteca x86 nativa para vinculação ao seu aplicativo|

  Para criar um aplicativo, copie ou mova os binários necessários (e bibliotecas) para o seu ambiente de desenvolvimento e inclua-os conforme necessário no seu processo de compilação.

### <a name="android"></a>Android

O SDK do Java para Android é empacotado como um [AAR (Biblioteca Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias, bem como as permissões necessárias do Android para usá-lo.
Ele está hospedado em um repositório Maven em `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0`.
Consumir o pacote do projeto do Android Studio faz as seguintes alterações:

* No arquivo `build.gradle` de nível do projeto, inclua o seguinte na seção `repository`:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* No arquivo `build.gradle` de nível de módulo, inclua o seguinte na seção `dependencies`:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.6.0'
  ```

O SDK do Java também faz parte do [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

[!include[Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
