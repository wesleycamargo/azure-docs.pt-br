---
title: Analisar os vídeos com os Serviços de Mídia do Azure | Microsoft Docs
description: Siga as etapas deste tutorial para analisar vídeos usando os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: 54c49645722b6545d8ae872151b9b82674d44523
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Tutorial: Analisar vídeos com os Serviços de Mídia do Azure 

Este tutorial mostra como analisar vídeos com os Serviços de Mídia do Azure. Há muitos cenários em que você talvez queira obter ideias profundas sobre vídeos gravados ou conteúdo de áudio. Por exemplo, para obter maior satisfação do cliente, as organizações podem executar processamento de fala para texto para converter gravações de suporte do cliente em um catálogo pesquisável com índices e painéis. Em seguida, podem obter ideias sobre seus negócios como uma lista de reclamações comuns, fontes de tais reclamações, etc.

Este tutorial mostra como:    

> [!div class="checklist"]
> * Iniciar o Azure Cloud Shell
> * Criar uma conta de Serviços de Mídia
> * Instalar a API de Serviços de Mídia
> * Configurar o aplicativo de exemplo
> * Examinar o código de exemplo em detalhes
> * Execute o aplicativo
> * Examinar a saída
> * Limpar recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos

Se o Visual Studio não estiver instalado, você poderá obter o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Baixar o exemplo

Clone um repositório do GitHub que contém o exemplo de .NET em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>Examinar o código de exemplo em detalhes

Esta seção examina funções definidas no arquivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) do projeto *AnalyzeVideos*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. Em primeiro lugar, você precisa obter um token e, em seguida, criar um objeto do **ClientCredential** retornado. No código clonado no início do artigo, o objeto **ArmClientCredential** é usado para obter o token.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Criar um ativo de saída para armazenar o resultado de um trabalho 

O ativo de saída armazena o resultado do seu trabalho. O projeto define a função **DownloadResults** que faz o download dos resultados desse ativo de saída para a pasta de "saída", para que você possa ver o que tem.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Criar uma transformação e um trabalho que analisa os vídeos

Ao codificar ou processar o conteúdo nos Serviços de Mídia do Microsoft Azure, é um padrão comum para configurar as configurações de codificação como uma receita. Em seguida, você pode enviar um **Trabalho** para aplicar essa fórmula a um vídeo. Ao enviar novos trabalhos para cada novo vídeo, você está aplicando essa fórmula em todos os vídeos na biblioteca. Uma receita nos Serviços de Mídia do Microsoft Azure é chamada de **Transformação**. Para obter mais informações, consulte [Transformações e Trabalhos](transform-concept.md). O exemplo descrito neste tutorial define uma receita que analisa o vídeo especificado. 

#### <a name="transform"></a>Transformar

Ao criar um novo exemplo de **Transformação**, você precisa especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, como mostrado no código acima. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. Neste exemplo, a predefinição **VideoAnalyzerPreset** é usada e o idioma ("en-US") é passado para o construtor. Esta predefinição permite extrair várias informações de áudio e vídeo de um vídeo. Você pode usar a predefinição **AudioAnalyzerPreset** se você precisa extrair as várias ideias de áudio de um vídeo. 

Ao criar uma **Transformação**, você deverá verificar primeiro se já existe uma usando o método **Get**, conforme mostrado no código a seguir.  Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Trabalho

Conforme mecionado acima, o objeto de **Transformação** é a receita e um **Trabalho** é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como o local da entrada vídeo e o local da saída. Você pode especificar o local do seu vídeo usando: URLs HTTPS, URLs da SAS ou ativos que estão em sua conta de Serviço de Mídia. 

Neste exemplo, a entrada de trabalho é um vídeo local.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

Este trabalho levará algum tempo para concluir e quando você desejar ser notificado. Há diferentes opções para ser notificado sobre a conclusão do trabalho. A opção mais simples (que é mostrada aqui) é usar a sondagem. 

Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos.

A Grade de Eventos foi projetada para alta disponibilidade, desempenho consistente e dimensionamento dinâmico. Com a Grade de Eventos, seus aplicativos podem escutar e reagir a eventos de praticamente todos os serviços do Azure, bem como de origens personalizadas. A manipulação de eventos simples e reativa baseada em HTTP ajuda você a criar soluções eficientes por meio da filtragem e do roteamento de eventos. Consulte [Rotear eventos para um ponto de extremidade personalizado de web](job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Em fila**, **Processamento**, **Concluído** (o estado final). Se o trabalho encontrou um erro, você receberá o estado do **Erro**. Se o trabalho está no processo de ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="download-the-result-of-the-job"></a>Faça o download do resultado do trabalho

A função a seguir realiza os downloads dos resultados do ativo de saída para a pasta de "saída", para que você possa examinar os resultados do trabalho. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Em geral, você deve limpar tudo, exceto os objetos que planeja reutilizar (normalmente, você reutilizará transformações e persistirá StreamingLocators, etc.). Se desejar para sua conta para ser limpa depois de testar, você deve excluir os recursos que não planeja reutilizar. Por exemplo, o código a seguir exclui trabalhos.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

Pressione CTRL+F5 para executar o aplicativo *AnalyzeVideos*.

Quando executamos o programa, o trabalho produz miniaturas de cada face que encontrar no vídeo. Ele também produz o arquivo insights.json.

## <a name="examine-the-output"></a>Examinar a saída

O arquivo de saída de análise de vídeos é chamado insights.json. Esse arquivo contém informações sobre o vídeo. Você pode encontrar uma descrição dos elementos encontrados no arquivo json no artigo [Inteligência de mídia](intelligence-concept.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente. Use a ferramenta **CloudShell**.

No **CloudShell**, execute o seguinte comando:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Os SDKs dos Serviços de Mídia do Azure v3 não são thread-safe. Ao trabalhar com aplicativos multithread, você deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
