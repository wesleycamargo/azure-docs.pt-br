---
title: Tutorial da API de Detecção de Emoções no C# | Microsoft Docs
description: Explore um aplicativo básico do Windows que usa a API de Detecção de Emoções dos Serviços Cognitivos para reconhecer as emoções expressas pelas faces em uma imagem.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/23/2017
ms.author: anroth
ms.openlocfilehash: f015e5720f65d0951a77de76ce8882a6dcdc1c3b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363711"
---
# <a name="emotion-api-in-c35-tutorial"></a>Tutorial da API de Detecção de Emoções no C&#35;

> [!IMPORTANT]
> A versão prévia da API de vídeo terminará em 30 de outubro de 2017. Experimente a nova [versão prévia da API do Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) para extrair facilmente as informações dos vídeos e aprimorar as experiências de descoberta de conteúdo, tais como resultados da pesquisa através da detecção de palavras faladas, faces, personagens e emoções. [Saiba mais](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Explore um aplicativo básico do Windows que usa a API de Detecção de Emoções para reconhecer as emoções expressas pelas faces em uma imagem. O exemplo abaixo permite que você envie uma URL de imagem ou um arquivo armazenado localmente. Use esse exemplo de software livre como modelo para criar seu próprio aplicativo para o Windows usando a API de Detecção de Emoções e o WPF (Windows Presentation Foundation), uma parte do .NET Framework.

## <a name="Prerequisites">Pré-requisitos</a>
#### <a name="platform-requirements"></a>Requisitos de plataforma  
O exemplo abaixo foi desenvolvido para o .NET Framework usando o [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Assinar a API de Detecção de Emoções e obter uma chave de assinatura  
Antes de criar o exemplo, é necessário assinar a API de Detecção de Emoções, que faz parte dos Serviços Cognitivos da Microsoft. Veja [Assinaturas](https://azure.microsoft.com/try/cognitive-services/). Tanto a chave primária quanto a secundária podem ser usadas neste tutorial. Lembre-se de seguir as melhores práticas para manter sua chave de API em segredo e segura.  

#### <a name="get-the-client-library-and-example"></a>Obter a biblioteca de clientes e o exemplo  
Baixe a biblioteca de clientes da API de Detecção de Emoções por meio do [SDK](https://www.github.com/microsoft/cognitive-emotion-windows). O arquivo zip baixado precisa ser extraído para uma pasta de sua escolha. Muitos usuários escolhem a pasta do Visual Studio 2015.
## <a name="Step1">Etapa 1: Abrir o exemplo</a>
1.  Inicie o Microsoft Visual Studio 2015 e clique em **Arquivo**, selecione **Abrir** e, em seguida, **Projeto/Solução**.
2.  Procure a pasta na qual você salvou os arquivos baixados da API de Detecção de Emoções. Clique em **Detecção de Emoções**, em **Windows** e, em seguida, na pasta **Sample-WPF**.
3.  Clique duas vezes para abrir o arquivo de Solução do Visual Studio 2015 (.sln) chamado **EmotionAPI-WPF-Samples.sln**. Isso abrirá a solução no Visual Studio.

## <a name="Step2">Etapa 2: Compilar o exemplo</a>
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências** e selecione **Gerenciar Pacotes NuGet**.

  ![Abrir o Gerenciador de Pacotes NuGet](../Images/EmotionNuget.png)

2.  A janela **Gerenciador de Pacotes NuGet** será aberta. Primeiro, selecione **Procurar** no canto superior esquerdo. Em seguida, na caixa de pesquisa, digite “Newtonsoft.Json”, selecione o pacote **Newtonsoft.Json** e clique em **Instalar**.  

  ![Procurar o pacote NuGet](../Images/EmotionNugetBrowse.png)  

3.  Pressione Ctrl+Shift+B, ou clique em **Compilar** no menu de faixa de opções e, em seguida, selecione **Compilar Solução**.

## <a name="Step3">Etapa 3: Executar o exemplo</a>
1.  Após a compilação, pressione **F5** ou clique em **Iniciar** no menu de faixa de opções para executar o exemplo.
2.  Localize a janela da API de Detecção de Emoções com a **caixa de texto** que indica "**Cole sua chave de assinatura aqui para começar**". Cole a chave de assinatura na caixa de texto, conforme mostrado na captura de tela abaixo. Você pode optar por persistir a chave de assinatura no computador ou no laptop clicando no botão "Salvar Chave". Quando desejar excluir a chave de assinatura do sistema, clique em "Excluir Chave" para removê-la do computador ou do laptop.
  
  ![Interface da funcionalidade de Detecção de Emoções](../Images/EmotionKey.png)

3.  Em "**Selecionar Cenário**", clique para usar um dos dois cenários, “**Detectar emoção usando um fluxo**” ou “**Detectar emoção usando uma URL**” e, em seguida, siga as instruções na tela. A Microsoft recebe as imagens de upload e pode usá-las para melhorar a API de Detecção de Emoções e os serviços relacionados. Ao enviar uma imagem, você confirma que seguiu nosso [Código de Conduta do Desenvolvedor](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Há imagens de exemplo a serem usadas com esse aplicativo de exemplo. Encontre essas imagens no [repositório GitHub da API de Detecção Facial](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) na pasta **Dados**. Observe que o uso dessas imagens é licenciado nos termos no contrato de Uso Justo, o que significa que é legal usar as imagens para testar esse exemplo, mas não para nova publicação.

## <a name="Review">Examinar e aprender</a>
Agora que você tem um aplicativo em execução, vamos examinar como esse aplicativo de exemplo é integrado aos Serviços Cognitivos da Microsoft. Isso facilitará para você continuar aproveitando esse aplicativo ou desenvolver seu próprio aplicativo usando a API de Detecção de Emoções da Microsoft. 

Esse aplicativo de exemplo usa a Biblioteca de Clientes da API de Detecção de Emoções, um wrapper de cliente fino C# para a API de Detecção de Emoções da Microsoft. Quando você compilou o aplicativo de exemplo, conforme descrito acima, você obteve a Biblioteca de Clientes de um pacote NuGet. Você pode examinar o código-fonte da Biblioteca de Clientes na pasta intitulada "[Biblioteca de Clientes](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)" em **Detecção de Emoções**, **Windows**, **Biblioteca de Clientes**, que faz parte do repositório de arquivo baixado, mencionado acima em [Pré-requisitos](#Prerequisites).
 
Descubra também como usar o código da Biblioteca de Clientes no **Gerenciador de Soluções**: em **EmotionAPI-WPF_Samples**, expanda **DetectEmotionUsingStreamPage.xaml** para localizar **DetectEmotionUsingStreamPage.xaml.cs**, que é usado para procurar um arquivo armazenado localmente, ou expanda **DetectEmotionUsingURLPage.xaml** para localizar **DetectEmotionUsingURLPage.xaml.cs**, que é usado ao fazer upload de uma URL de imagem. Clique duas vezes nos arquivos .xaml.cs para abri-los em novas janelas do Visual Studio. 

Examinando como a Biblioteca de Clientes da Detecção de Emoções é usada em nosso aplicativo de exemplo, vamos dar uma olhada em dois trechos de código de **DetectEmotionUsingStreamPage.xaml.cs** e **DetectEmotionUsingURLPage.xaml.cs**. Cada arquivo contém comentários sobre código indicando “O CÓDIGO DE EXEMPLO DE CHAVE COMEÇA AQUI” e “O CÓDIGO DE EXEMPLO DE CHAVE TERMINA AQUI” para ajudá-lo a localizar os trechos de código reproduzidos abaixo.

A API de Detecção de Emoções pode trabalhar com os dados de uma URL de imagem ou de uma imagem binária (na forma de um fluxo de octeto) como entrada. As duas opções são examinadas abaixo. Em ambos os casos, primeiro você encontrar uma usando uma diretiva, que permite o uso da Biblioteca de Clientes de Detecção de Emoções. 
```csharp

            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE STARTS HERE 
            // Use the following namespace for EmotionServiceClient 
            // ----------------------------------------------------------------------- 
            using Microsoft.ProjectOxford.Emotion; 
            using Microsoft.ProjectOxford.Emotion.Contract; 
            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------- 
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs 

Este trecho de código mostra como usar a Biblioteca de Clientes para enviar sua chave de assinatura e uma URL de foto para o serviço API de Detecção de Emoções. 

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs 

O exemplo abaixo mostra como enviar sua chave de assinatura e uma imagem armazenada localmente para a API de Detecção de Emoções. 


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
