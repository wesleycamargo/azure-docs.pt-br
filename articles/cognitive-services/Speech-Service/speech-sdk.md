---
title: Sobre o SDK de Fala de Serviços Cognitivos | Microsoft Docs
description: Uma visão geral dos SDKs disponíveis para o serviço de Fala.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: b9b7b8af5ce3d75788fd2c4f5e0309b5ca561a8f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364773"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Sobre o SDK de Fala de Serviços Cognitivos

O SDK (Software Development Kit) de Fala de Serviços Cognitivos fornece aos seus aplicativos acesso nativo às funções do serviço de Fala, tornando mais fácil desenvolver software. No momento, o SDK dá acesso a **Conversão de Fala em Texto**, **Tradução de Fala** e **Reconhecimento de Intenção**.

A tabela lista as linguagens de programação e os sistemas operacionais compatíveis no momento.

|Sistema operacional com suporte|Linguagem de programação|
|-|-|
|Windows|C/C++, C#|
|Linux|C/C++|
|Dispositivos|Java\*|

\* *O SDK do Java faz parte do [SDK dos Dispositivos de Fala](speech-devices-sdk.md).*

## <a name="get-the-windows-sdk"></a>Obter o SDK do Windows

A versão do Windows do SDK de Fala inclui bibliotecas de cliente em C/C++ de 32 bits e 64 bits, bem como bibliotecas gerenciadas (.NET) para uso com o C#. O SDK pode ser instalado no Visual Studio usando NuGet; simplesmente pesquise `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>Obtenha o SDK do Linux

Verifique se você tem o compilador e as bibliotecas necessários executando os seguintes comandos do shell:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> Essas instruções presumem que você está executando Ubuntu 16.04 em um PC (x86 ou x64). Em uma versão diferente do Ubuntu, ou em uma distribuição diferente do Linux, adapte estas etapas ao seu ambiente.

Em seguida, [baixe o SDK](https://aka.ms/csspeech/linuxbinary) e desempacote os arquivos em um diretório de sua escolha. Esta tabela mostra a estrutura de pastas do SDK.

|Caminho|DESCRIÇÃO|
|-|-|
|`license.md`|Licença|
|`third-party-notices.md`|Avisos de terceiros|
|`include`|Arquivos de cabeçalho C e C++|
|`lib/x64`|Biblioteca x64 nativa para vinculação ao seu aplicativo|
|`lib/x86`|Biblioteca x86 nativa para vinculação ao seu aplicativo|

Para criar um aplicativo, copie ou mova os binários necessários (e bibliotecas) para o seu ambiente de desenvolvimento e inclua-os conforme necessário no seu processo de compilação.

## <a name="get-the-java-sdk"></a>Obter o SDK do Java

O SDK do Java faz parte do [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstart-csharp-windows.md)
