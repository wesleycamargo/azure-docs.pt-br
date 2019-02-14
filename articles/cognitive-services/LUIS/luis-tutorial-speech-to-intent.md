---
title: Usar o SDK de Fala em C#
titleSuffix: Azure Cognitive Services
description: O serviço de Fala permite que você use uma única solicitação para receber áudio e retornar objetos JSON de previsão do LUIS. Neste artigo, você pode baixar e usar um projeto C# no Visual Studio para falar uma expressão em um microfone e receber informações de previsão LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: c247ef748628c14074c2b5ea37df251793840b16
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876027"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>Integrar o Serviço de Fala ao seu aplicativo de Reconhecimento vocal
O [Serviço de Fala](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) permite que você use uma única solicitação para receber áudio e retornar objetos JSON de previsão LUIS. Neste artigo, você pode baixar e usar um projeto C# no Visual Studio para falar uma expressão em um microfone e receber informações de previsão LUIS. O projeto usa o pacote [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) de Fala, já está incluído como referência. 

Para este artigo, você precisa de uma conta de website gratuita de [LUIS][LUIS] para importar aplicativo.

## <a name="create-luis-endpoint-key"></a>Criar chave de ponto de extremidade LUIS
No portal do Azure, [crie](luis-how-to-azure-subscription.md) uma chave de **Serviço Inteligente de Reconhecimento Vocal** (LUÍS). 

## <a name="import-human-resources-luis-app"></a>Importar aplicativo LUIS de Recursos Humanos
As intenções e os enunciados deste artigo foram retirados do aplicativo LUIS de Recursos Humanos disponível no repositório GitHub [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Baixe o arquivo [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json), salve-o com a extensão `.json` e [importe](luis-how-to-start-new-app.md#import-new-app)-o no LUIS. 

Este aplicativo tem intenções, entidades e expressões relacionadas com o domínio Recursos Humanos. Expressões de exemplo incluem:

|Exemplo de enunciados|
|--|
|Quem é o gerente de Pedro Gonçalves?|
|Quem Pedro Gonçalves gerencia?|
|Onde está o formulário 123456?|
|Eu tenho alguma folga paga?|


## <a name="add-keyphrase-prebuilt-entity"></a>Adicionar entidade predefinida KeyPhrase
Depois de importar o aplicativo, selecione **Entidades**, em seguida, **Adicionar entidade predefinida**. Adicionar a entidade **KeyPhrase**. A entidade KeyPhrase extrai a chave sujeita ao assunto da expressão.

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
 Clone ou baixe o repositório [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Abra o [projeto Conversão de fala em intenção](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition) com o Visual Studio e restaure os pacotes do NuGet. O arquivo de solução do VS é documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

O SDK de Fala já está incluído como uma referência. 

[![Captura de tela do Visual Studio 2017 exibindo o pacote NuGet Microsoft.CognitiveServices.Speech](./media/luis-tutorial-speech-to-intent/nuget-package.png "Captura de tela do Visual Studio 2017 exibindo o pacote NuGet Microsoft.CognitiveServices.Speech")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Modifique o código C#
Abra o arquivo `Program.cs` e altere as variáveis a seguir:

|Nome da variável|Finalidade|
|--|--|
|LUIS_assigned_endpoint_key|Corresponde ao valor de chave de assinatura atribuído à URL do ponto de extremidade da página de publicação|
|LUIS_endpoint_key_region|Corresponde ao primeiro subdomínio da URL do ponto de extremidade, por exemplo, `westus`|
|LUIS_app_ID|Corresponde a rota da URL de ponto de extremidade depois de **aplicativos/**|

O arquivo `Program.cs` já tem as intenções de recursos humanos mapeadas.

Compile e execute o aplicativo. 

## <a name="test-code-with-utterance"></a>Código de teste com expressões
Fale no microfone "Quem são os dentistas aprovados em Redmond?".

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

A intenção correta, **GetEmployeeBenefits**, foi encontrada com 85% de fiabilidade. A entidade KeyPhrase foi retornada. 

O SDK de Fala retorna a resposta LUIS inteira. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo Recursos Humanos de LUIS. Para fazer isso, selecione o aplicativo e, em seguida, na barra de ferramentas contextual acima da lista, selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

Lembre-se de excluir o diretório quando terminar de usar o código de exemplo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Integrar LUIS com um BOT](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
