---
title: 'Início Rápido: Reconhecer fala em JavaScript no Node.js usando o SDK do Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em JavaScript em um Node.js usando o SDK do Serviço de Fala
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 35652b169067bc545fa0d1fcc977bbaee79ec3aa
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724414"
---
# <a name="quickstart-recognize-speech-in-javascript-in-nodejs-using-the-speech-service-sdk"></a>Início Rápido: Reconhecer fala em JavaScript no Node.js usando o SDK do Serviço de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá como criar um projeto do Node.js usando a associação JavaScript do SDK de Fala de Serviços Cognitivos para transcrever conversão de fala em texto.
O aplicativo é baseado no [Speech SDK de Serviços Cognitivos](https://aka.ms/csspeech/npmpackage) da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* Uma versão atual do [Node.js](https://nodejs.org).

## <a name="create-a-new-project-folder"></a>Crie uma nova pasta de projeto

Crie uma nova pasta vazia e inicialize-a como um novo projeto JavaScript e Node.js.

```sh
npm init -f
```

Isso iniciará os arquivos package.json com valores padrão. Você provavelmente desejará editar esse arquivo mais tarde.

## <a name="install-the-speech-sdk-for-javascript-into-that-folder"></a>Instale o Speech SDK para JavaScript nessa pasta

Adicione o Speech SDK por meio de `npm install microsoft-cognitiveservices-speech-sdk` ao seu projeto do Node.js.

Isso vai baixar e instalar a versão mais recente do Speech SDK e qualquer pré-requisito necessário do npmjs. O SDK será instalado no diretório `node_modules` dentro de sua pasta de projeto.

## <a name="using-the-speech-sdk"></a>Usando o Speech SDK

Crie um novo arquivo na pasta, chamada `index.js`, e abra este arquivo com um editor de texto.

> [!NOTE]
> Observe que, no Node.js, o Speech SDK não dá suporte ao tipo de dados de Arquivo ou microfone. Ambos têm suporte apenas em navegadores. Em vez disso, use a interface de Stream para o Speech SDK seja por meio de `AudioInputStream.createPushStream()` ou de `AudioInputStream.createPullStream()`.

Neste exemplo, usaremos a interface `PushAudioInputStream`.

Adicione o seguinte código JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="running-the-sample-from-command-line"></a>Executando o exemplo de linha de comando

Para iniciar o aplicativo, adapte `YourSubscriptionKey`, `YourServiceRegion` e `YourAudioFile.wav` à sua configuração. Em seguida, você poderá executá-lo chamando o comando a seguir:

```sh
node index.js
```

Isso disparará um reconhecimento usando o nome de arquivo fornecido e apresentará a saída no console.

Aqui está um exemplo de saída da execução de `index.js` depois de atualizar a chave de assinatura e usar o arquivo `whatstheweatherlike.wav`.

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="running-the-sample-from-visual-studio-code"></a>Executando o exemplo do Visual Studio Code

Você também pode executar o exemplo do Visual Studio Code. Siga estas etapas para instalar, abrir e executar o início rápido:

1. Inicie o Visual Studio Code e clique em "Abrir Pasta", então navegue até a pasta de início rápido

   ![Captura de tela de Abrir Pasta](media/sdk/qs-js-node-01-open_project.png)

1. Abrir um terminal no Visual Studio Code

   ![Captura de tela da janela do terminal](media/sdk/qs-js-node-02_open_terminal.png)

1. Execute o npm para instalar as dependências

   ![Captura de tela de instalação de npm](media/sdk/qs-js-node-03-npm_install.png)

1. Agora você está pronto para abrir `index.js` e definir um ponto de interrupção

   ![Captura de tela de index.js com um ponto de interrupção na linha 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Para iniciar a depuração, pressione F5 ou selecione Depurar/Iniciar Depuração no menu

   ![Captura de tela do menu de depurar](media/sdk/qs-js-node-05-start_debugging.png)

1. Quando um ponto de interrupção é atingido, você pode inspecionar a pilha de chamadas e variáveis

   ![Captura de tela do depurador](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Qualquer saída será mostrada na janela do console de depuração

   ![Captura de tela do console de depuração](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar exemplos do Node.js no GitHub](https://aka.ms/csspeech/samples)
