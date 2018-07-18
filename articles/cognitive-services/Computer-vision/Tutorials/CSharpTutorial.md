---
title: Tutorial da API da Pesquisa Visual Computacional no C# | Microsoft Docs
description: Explore um aplicativo Windows básico que usa a API da Pesquisa Visual Computacional nos Serviços Cognitivos da Microsoft. Execute o OCR, crie miniaturas e trabalhe com recursos visuais em uma imagem.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363855"
---
# <a name="computer-vision-api-c35-tutorial"></a>API da Pesquisa Visual Computacional C&#35; Tutorial

Explore um aplicativo Windows que usa a API da Pesquisa Visual Computacional para executar o reconhecimento óptico de caracteres (OCR), criar miniaturas com recorte inteligente, além de detectar, categorizar, marcar e descrever recursos visuais, incluindo rostos, em uma imagem. O exemplo abaixo permite que você envie uma URL de imagem ou um arquivo armazenado localmente. Use esse exemplo de software livre como modelo para criar seu próprio aplicativo para o Windows usando a API de Visão e o WPF (Windows Presentation Foundation), uma parte do .NET Framework.

### <a name="prerequisites"></a>pré-requisitos

#### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo abaixo foi desenvolvido para o .NET Framework usando o [Visual Studio 2015, Community Edition](https://www.visualstudio.com/downloads/).

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Inscrever-se na API da Pesquisa Visual Computacional e obter uma chave de assinatura 

Antes de criar o exemplo, é necessário assinar a API da Pesquisa Visual Computacional, que faz parte dos Serviços Cognitivos da Microsoft (antigo Projeto Oxford). Para detalhes de assinatura e gerenciamento de chaves, consulte [Assinaturas](https://azure.microsoft.com/try/cognitive-services/). Tanto a chave primária quanto a secundária podem ser usadas neste tutorial. 

> [!NOTE]
> O tutorial foi desenvolvido para usar chaves de assinatura na região **westcentralus**. As chaves de assinatura geradas na avaliação gratuita da Pesquisa Visual Computacional usa a região **westcentralus**, para que elas funcionem corretamente. Se você gerou suas chaves de assinatura usando sua conta do Azure por meio de [https://azure.microsoft.com/](https://azure.microsoft.com/), você deve especificar a região **westcentralus**. Chaves geradas fora da região **westcentralus** não funcionarão.

#### <a name="get-the-client-library-and-example"></a>Obter a biblioteca de clientes e o exemplo

Você pode clonar o aplicativo de exemplo e a biblioteca de clientes da API da Pesquisa Visual Computacional em seu computador por meio de [SDK](https://www.github.com/microsoft/cognitive-vision-windows). Não faça o download como um arquivo ZIP.

### <a name="Step1">Etapa 1: Instalar o exemplo</a>

Em sua área de trabalho do GitHub, abra Sample-WPF\VisionAPI-WPF-Samples.sln.

### <a name="Step2">Etapa 2: Compilar o exemplo</a>

* Pressione Ctrl+Shift+B, ou clique em Compilar no menu de faixa de opções, então selecione Compilar Solução.

### <a name="Step3">Etapa 3: Executar o exemplo</a>

1. Após a compilação, pressione **F5** ou clique em **Iniciar** no menu de faixa de opções para executar o exemplo.
2. Localize a janela da interface do usuário da API de Pesquisa Visual Computacional com a caixa de texto que indica “Cole sua chave de assinatura aqui para começar”.
Você pode optar por persistir a chave de assinatura no computador ou no laptop clicando no botão "Salvar Chave". Quando desejar excluir a chave de assinatura do sistema, clique em "Excluir Chave" para removê-la do computador ou do laptop.

    ![Chave de assinatura da Pesquisa Visual Computacional](../Images/Vision_UI_Subscription.PNG)

3. Em "Selecionar cenário" clique para usar um dos seis cenários e siga as instruções na tela. A Microsoft recebe as imagens carregadas e pode usá-las para melhorar a API de Pesquisa Visual Computacional e os serviços relacionados. Ao enviar uma imagem, você confirma que seguiu nosso [Código de Conduta do Desenvolvedor](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).

    ![Analisar a Interface da imagem](../Images/Analyze_Image_Example.PNG)

4. Há imagens de exemplo a serem usadas com esse aplicativo de exemplo. Encontre essas imagens no repositório GitHub da API de Detecção Facial na [pasta Dados](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data). Observe que o uso dessas imagens é licenciado sob o contrato [LICENÇA-IMAGEM](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md).

### <a name="Review">Analisar e aprender</a>

Agora que você tem um aplicativo pronto para execução, vamos analisar como o aplicativo de exemplo se integra com a tecnologia de Serviços Cognitivos. Isso facilitará para você continuar aproveitando esse aplicativo ou desenvolver seu próprio aplicativo usando a API de Pesquisa Visual Computacional da Microsoft.

Esse aplicativo de exemplo usa a Biblioteca de Clientes da API de Pesquisa Visual Computacional, um wrapper de cliente fino C# para a API de Em sua área de trabalho do GitHub, abra da Microsoft. Quando você compilou o aplicativo de exemplo, conforme descrito acima, você obteve a Biblioteca de Clientes de um pacote NuGet. Você pode examinar o código-fonte da Biblioteca de Clientes na pasta intitulada “**Biblioteca de Clientes**” em **Pesquisa Visual Computacional**, **Windows**, **Biblioteca de Clientes**, que faz parte do repositório de arquivo baixado, mencionado acima em Pré-requisitos.

Você também pode descobrir como usar o código de Biblioteca de Clientes no Gerenciador de Soluções: em **VisionAPI WPF_Samples**, expanda **AnalyzePage.xaml** para localizar **AnalyzePage.xaml.cs**, que é usado para enviar uma imagem para o ponto de extremidade de análise de imagem. Clique duas vezes nos arquivos .xaml.cs para abri-los em novas janelas do Visual Studio.

Vamos examinar como a Biblioteca de Clientes Pesquisa Visual Computacional é usada em nosso aplicativo de exemplo e ver dois trechos de código do **AnalyzePage.xaml.cs**. O arquivo contém comentários de código indicando "O CÓDIGO DE EXEMPLO DE CHAVE COMEÇA AQUI" e "O CÓDIGO DE EXEMPLO DE CHAVE TERMINA AQUI" para ajudá-lo a localizar os trechos de código reproduzidos abaixo.

O ponto de extremidade da análise pode trabalhar com dados de uma URL de imagem ou de uma imagem binária (na forma de um fluxo de octeto) como entrada. Primeiro você encontra uma usando uma diretiva, que permite o uso da Biblioteca de Clientes de Pesquisa Visual Computacional.

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(...)**  Este trecho de código mostra como usar a Biblioteca de Clientes para enviar a sua chave de assinatura e uma imagem armazenada localmente para o ponto de extremidade de análise do serviço da API de Pesquisa Visual Computacional.

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(...)**  Este trecho de código mostra como usar a Biblioteca de Clientes para enviar a sua chave de assinatura e a URL de uma foto para o ponto de extremidade de análise do serviço da API de Pesquisa Visual Computacional.

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**Outras páginas e pontos de extremidade** Como interagir com os outros pontos de extremidade expostos pelo serviço de API de Pesquisa Visual Computacional pode ser visto examinando as outras páginas no exemplo; por exemplo, o ponto de extremidade da OCR é mostrado como parte do código contido em OCRPage.xaml.cs 

### <a name="Related">Tópicos relacionados</a>
 * [Introdução à API de Detecção Facial](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


