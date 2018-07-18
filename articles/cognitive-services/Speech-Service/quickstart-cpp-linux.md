---
title: Início rápido do SDK de Fala para C++ e Linux | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente o SDK de Fala com Linux e C++ em Serviços Cognitivos.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: cee70ba585f93dda3249fc5b39f25fb613b57a45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753568"
---
# <a name="quickstart-for-c-and-linux"></a>Início rápido para C++ e Linux

A versão atual do SDK de Fala dos Serviços Cognitivos é `0.4.0`.

O SDK de Fala dos Serviços Cognitivos para Linux está disponível para criação de aplicativos de 64 bits e 32 bits. Os arquivos necessários podem ser baixados como um arquivo tar de https://aka.ms/csspeech/linuxbinary.

> [!NOTE]
> Se você estiver procurando um início rápido para C# e Windows, acesse [aqui](quickstart-cpp-windows.md).
> Se você estiver procurando um início rápido para C# e Windows, acesse [aqui](quickstart-csharp-windows.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Estas instruções presumem que você está rodando no Ubuntu 16.04 em um PC (x86 ou x64).
> Em uma versão diferente do Ubuntu, ou em uma distribuição Linux diferente, você terá que adaptar as etapas necessárias.

## <a name="prerequisites"></a>pré-requisitos

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>Obtenção de pacote binário

[!include[License Notice](includes/license-notice.md)]

1. Escolha um diretório (caminho absoluto) onde você gostaria de colocar os cabeçalhos e os binários do SDK de fala.
   Por exemplo, escolha o caminho `speechsdk` em seu diretório inicial:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório, se ainda não existir:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Realize o download e extraia o `.tar.gz` arquivo com os binários SDK de Fala:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Valide o conteúdo do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Deve mostrar arquivos de notificação e licença de terceiros, bem como um diretório `include` para cabeçalhos e um diretório `lib` para bibliotecas.

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>Exemplo de código

O código a seguir reconhece a fala em inglês do seu microfone.
Coloque-o em um arquivo chamado `quickstart-linux.cpp`:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

Substitua a chave de assinatura no código pela que você obteve.

## <a name="building"></a>Construção

> [!NOTE]
> Certifique-se de copiar e colar os comandos de compilação abaixo como uma _única linha_.

* Execute o seguinte comando para construir o aplicativo em uma máquina x64:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Execute o seguinte comando para construir o aplicativo em uma máquina x86:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>Executando

Para executar o aplicativo, é necessário configurar o caminho da biblioteca do carregador para apontar para a biblioteca do SDK de Fala.

* Em uma máquina x64, execute:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* Em uma máquina x86, execute:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

Execute o aplicativo conforme a seguir:

```sh
./quickstart-linux
```

Se tudo correr bem, você deverá ver uma saída semelhante a esta:

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>Baixar o exemplo

Para obter o último conjunto de exemplos, consulte o [Repositório GitHub de exemplo do SDK de Fala dos Serviços Cognitivos](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Próximas etapas

* Visita a [página de exemplos](samples.md) para obter exemplos adicionais.
