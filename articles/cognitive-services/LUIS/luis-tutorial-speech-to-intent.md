---
title: Usar o SDK de Fala C# com o LUIS
titleSuffix: Azure Cognitive Services
description: O serviço de Fala permite que você use uma única solicitação para receber áudio e retornar objetos JSON de previsão do LUIS. Neste artigo, você pode baixar e usar um projeto C# no Visual Studio para falar uma expressão em um microfone e receber informações de previsão LUIS. O projeto usa o pacote NuGet de Fala, já incluído como uma referência.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 14956fd716a6939d5e7dd9d670cc78b58adf7f45
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042067"
---
# <a name="integrate-speech-service"></a>Integrar Serviço de Fala
O [Serviço de Fala](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) permite que você use uma única solicitação para receber áudio e retornar objetos JSON de previsão LUIS. Neste artigo, você pode baixar e usar um projeto C# no Visual Studio para falar uma expressão em um microfone e receber informações de previsão LUIS. O projeto usa o pacote [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) de Fala, já está incluído como referência. 

Para este artigo, você precisa de uma conta de website gratuita de [LUIS][LUIS] para importar aplicativo.

## <a name="create-luis-endpoint-key"></a>Criar chave de ponto de extremidade LUIS
No portal do Azure, [crie](luis-how-to-azure-subscription.md#create-luis-endpoint-key) uma chave de **Serviço Inteligente de Reconhecimento Vocal** (LUÍS). 

## <a name="import-human-resources-luis-app"></a>Importar aplicativo LUIS de Recursos Humanos
As intenções e expressões para este artigo são do aplicativo LUIS de Recursos Humanos disponível no repositório Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples). Baixe o arquivo [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json), salve-o com a extensão *.json, e [importe](luis-how-to-start-new-app.md#import-new-app) em LUIS. 

Este aplicativo tem intenções, entidades e expressões relacionadas com o domínio Recursos Humanos. Expressões de exemplo incluem:

|Exemplo de enunciados|
|--|
|Quem é o gerente de Pedro Gonçalves?|
|Quem Pedro Gonçalves gerencia?|
|Onde está o formulário 123456?|
|Eu tenho alguma folga paga?|


## <a name="add-keyphrase-prebuilt-entity"></a>Adicionar entidade predefinida KeyPhrase
Depois de importar o aplicativo, selecione **Entidades**, em seguida, **Gerenciar entidades predefinidas**. Adicionar a entidade **KeyPhrase**. A entidade KeyPhrase extrai a chave sujeita ao assunto da expressão.

## <a name="train-and-publish-the-app"></a>Treinar e publicar o aplicativo
1. Na barra de navegação superior direita, selecione o botão **Treinar** para treinar o aplicativo LUIS.

2. Selecione **Gerenciar** na barra superior direita e, em seguida, selecione **Chaves e pontos de extremidade** no painel de navegação à esquerda. 

3. Na página **Chaves e pontos de extremidade**, atribua a chave do LUIS criada na seção [Criar chave do ponto de extremidade do LUIS](#create-luis-endpoint-key).

  Nessa página, colete a ID do aplicativo, publique a região e a ID de assinatura da chave do LUIS criada na seção [Criar chave do ponto de extremidade do LUIS](#create-luis-endpoint-key). Você precisa modificar o código para usar esses valores posteriormente neste artigo. 
  
  **Não** use a chave gratuita de starter para este exercício. Somente uma chave de **Reconhecimento vocal** criada no portal do Azure funcionará para este exercício. 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=


4. Publique o aplicativo do LUIS selecionando o botão **Publicar** na barra superior direita. 

## <a name="audio-device"></a>Dispositivo de áudio
Este artigo usa o dispositivo de áudio no seu computador. Isso pode ser um fone de ouvido com microfone ou um dispositivo de áudio interno. Verifique os níveis de entrada de áudio para ver se você deve falar mais alto do que faria normalmente para que sua fala seja detectada pelo dispositivo de áudio. 

## <a name="download-the-luis-sample-project"></a>Baixar o projeto de exemplo LUIS
 Clone ou baixe o repositório [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples). Abra o [projeto Conversão de fala em intenção](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) com o Visual Studio e restaure os pacotes do NuGet. O arquivo de solução VS é .\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

O SDK de Fala já está incluído como uma referência. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Captura de tela do Visual Studio 2017 exibindo o pacote NuGet Microsoft.CognitiveServices.Speech")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Modifique o código C#
Abra o arquivo **LUIS_samples.cs** e altere as seguintes variáveis:

|Nome da variável|Finalidade|
|--|--|
|luisSubscriptionKey|Corresponde ao valor chave de assinatura da URL do ponto de extremidade da página de publicação|
|luisRegion|Corresponde ao primeiro domínio da URL do ponto de extremidade|
|luisAppId|Corresponde a rota da URL de ponto de extremidade depois de **aplicativos/**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Captura de tela do Visual Studio 2017 mostrando as variáveis de LUIS_samples.cs")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

O arquivo já tem as intenções de recursos humanos mapeadas.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Captura de tela do Visual Studio 2017 mostrando as intenções de LUIS_samples.cs")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Compile e execute o aplicativo. 

## <a name="test-code-with-utterance"></a>Código de teste com expressões
Selecione **1** e fale no microfone "Quem é o gerente de John Smith".

```cmd
1. Speech recognition of LUIS intent.
0. Stop.
Your choice: 1
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

A intenção correta, **GetEmployeeOrgChart**, foi encontrada com 61% de fiabilidade. A entidade KeyPhrase foi retornada. 

O SDK de Fala retorna a resposta LUIS inteira. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo Recursos Humanos de LUIS. Para fazer isso, selecione o botão de reticências (***...***) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

Lembre-se de excluir o diretório de exemplos LUIS quando você terminar de usar o código de exemplo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Integrar LUIS com um BOT](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website