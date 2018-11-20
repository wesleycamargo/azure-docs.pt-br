---
title: Carregar, codificar e transmitir usando serviços de Mídia do Azure | Microsoft Docs
description: Siga as etapas deste tutorial para carregar um arquivo e codificar o vídeo e transmitir o conteúdo com os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: d2d648dd02426596e1c070ffd494458c68060085
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613436"
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Tutorial: carregar, codificar e transmitir vídeos usando APIs

Os Serviços de Mídia do Azure permitem codificar os arquivos de mídia em formatos que podem ser reproduzidos em uma ampla variedade de navegadores e dispositivos. Por exemplo, talvez você queira transmitir por streaming o conteúdo nos formatos MPEG-DASH ou HLS da Apple. Antes do streaming, será necessário codificar o arquivo de mídia digital de alta qualidade. Para obter diretrizes de codificação, consulte [Conceito de codificação](encoding-concept.md). Este tutorial carrega um arquivo de vídeo local e codifica o arquivo carregado. Também é possível codificar o conteúdo disponibilizado por meio de uma URL HTTPS. Para mais informações, consulte [Criar uma entrada de trabalho de uma URL HTTP(s)](job-input-from-http-how-to.md).

![Reproduzir o vídeo](./media/stream-files-tutorial-with-api/final-video.png)

Este tutorial mostra como:    

> [!div class="checklist"]
> * Instalar a API de Serviços de Mídia
> * Configurar o aplicativo de exemplo
> * Examine o código que carrega, codifica e transmite
> * Execute o aplicativo
> * Testar a URL de streaming
> * Limpar recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se o Visual Studio não estiver instalado, você poderá obter o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- Instalar e usar a CLI localmente, este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` descobrir a versão que você tem. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

    Atualmente, nem todos os comandos da [CLI V3 dos Serviços de Mídia](https://aka.ms/ams-v3-cli-ref) funcionam no Azure Cloud Shell. É recomendável usar a CLI localmente.

- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

    Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia

## <a name="download-the-sample"></a>Baixar o exemplo

Clone um repositório do GitHub que contém o exemplo de streaming de .NET em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo está localizado na pasta [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Examine o código que carrega, codifica e transmite

Esta seção examina funções definidas no arquivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) do projeto *UploadEncodeAndStreamFiles*.

O exemplo executa as ações a seguir:

1. Cria uma nova transformação (primeiro, verifica se a transformação especificada existe). 
2. Cria um ativo de saída que é usado como a saída do trabalho de codificação.
3. Cria um ativo de entrada e carrega o arquivo de vídeo local especificado nele. O ativo é usado como entrada do trabalho. 
4. Envia o trabalho de codificação usando a entrada e saída criadas.
5. Verifica o status do trabalho.
6. Cria um StreamingLocator.
7. Compila as URLs de streaming.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um ativo de entrada e carregar um arquivo local para ele 

A função **CreateInputAsset** cria um novo [Ativo](https://docs.microsoft.com/rest/api/media/assets) de entrada e carrega o arquivo de vídeo local especificado para ele. Este Ativo é usado como entrada para o trabalho de codificação. Em Serviços de Mídia v3, a entrada para um trabalho pode ser um ativo ou pode ser o conteúdo que você disponibiliza a sua conta do Serviços de Mídia por meio de URLs de HTTPS. Se você quiser saber como codificar a partir de uma URL HTTPS, consulte [este](job-input-from-http-how-to.md) artigo.  

Em Serviços de Mídia v3, você usará as APIs de Armazenamento do Microsoft Azure para carregar os arquivos. O snippet .NET a seguir mostra como.

A função a seguir realiza essas ações:

* Cria um ativo 
* Obtém uma [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) gravável ao contêiner [do Ativo no armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Carrega o arquivo para o contêiner no armazenamento usando a URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Criar um ativo de saída para armazenar o resultado de um trabalho 

O [Ativo](https://docs.microsoft.com/rest/api/media/assets) de saída armazena o resultado do trabalho de codificação. O projeto define a função **DownloadResults** que faz o download dos resultados desse ativo de saída para a pasta de "saída", para que você possa ver o que tem.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Criar uma transformação e um trabalho que codifica o arquivo carregado
Ao codificar ou processar o conteúdo nos Serviços de Mídia do Microsoft Azure, é um padrão comum para configurar as configurações de codificação como uma receita. Em seguida, você pode enviar um **Trabalho** para aplicar essa fórmula a um vídeo. Ao enviar novos trabalhos para cada novo vídeo, você está aplicando essa fórmula em todos os vídeos na biblioteca. Uma receita nos Serviços de Mídia do Microsoft Azure é chamada de **Transformação**. Para obter mais informações, consulte [Transformações e Trabalhos](transform-concept.md). O exemplo descrito neste tutorial define uma receita que codifica o vídeo para transmitir para uma variedade de dispositivos de iOS e Android. 

#### <a name="transform"></a>Transformar

Ao criar um novo exemplo de [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você precisa especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, como mostrado no código a seguir. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. O exemplo descrito neste artigo usa uma predefinição chamada **AdaptiveStreaming**. A predefinição codifica o vídeo de entrada em uma escada de taxa de bits gerada automaticamente (pares de resolução de taxa de bits) com base na taxa de bits e resolução de entrada e produz arquivos ISO MP4 com vídeo H. 264 e correspondente a cada par de resolução de taxa de bits de áudio AAC. Para obter informações sobre essa predefinição, consulte [escada de bitrate de geração automática](autogen-bitrate-ladder.md).

Você pode usar um EncoderNamedPreset interno ou usar as predefinições personalizadas. Para obter mais informações, consulte [Como personalizar predefinições do codificador](customize-encoder-presets-how-to.md).

Ao criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você deverá verificar primeiro se já existe uma usando o método **Get**, conforme mostrado no código a seguir.  Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Trabalho

Conforme mecionado acima, o objeto de [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como o local da entrada vídeo e o local da saída.

Neste exemplo, o vídeo de entrada foi carregado a partir de sua máquina local. Se você quiser saber como codificar a partir de uma URL HTTPS, consulte [este](job-input-from-http-how-to.md) artigo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

Este trabalho levará algum tempo para concluir e quando você desejar ser notificado. O exemplo de código a seguir mostra como sondar o serviço para o status do [Trabalho](https://docs.microsoft.com/rest/api/media/jobs). Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos.

A Grade de Eventos foi projetada para alta disponibilidade, desempenho consistente e dimensionamento dinâmico. Com a Grade de Eventos, seus aplicativos podem escutar e reagir a eventos de praticamente todos os serviços do Azure, bem como de origens personalizadas. A manipulação de eventos simples e reativa baseada em HTTP ajuda você a criar soluções eficientes por meio da filtragem e do roteamento de eventos.  Consulte [Rotear eventos para um ponto de extremidade personalizado de web](job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Em fila**, **Processamento**, **Concluído** (o estado final). Se o trabalho encontrou um erro, você receberá o estado do **Erro**. Se o trabalho está no processo de ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="get-a-streaminglocator"></a>Obter um StreamingLocator

Depois que a codificação for concluída, a próxima etapa é tornar no vídeo no ativo de saída disponível para os clientes para reprodução. Você pode fazer isso em duas etapas: primeiro, crie um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)e em seguida, crie as URLs de streaming que os clientes podem usar. 

O processo de criação de um **StreamingLocator** é chamado de publicação. Por padrão, o **StreamingLocator** é válido imediatamente após você fazer as chamadas de API e dura até ser excluído, a menos que você configure os horários de início e término opcionais. 

Ao criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), você precisará especificar os detalhes desejados **StreamingPolicyName**. Neste exemplo, você fará o streaming limpo (ou conteúdo não criptografado), de modo que a política de streaming clara predefinida (**PredefinedStreamingPolicy.ClearStreamingOnly**) seja utilizada.

> [!IMPORTANT]
> Ao usar a [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizada, você deve criar um conjunto limitado de tais políticas para sua conta de serviço de mídia e reutilizá-los para o seu StreamingLocators, sempre que a mesmo opção de criptografia e protocolos sejam necessários. Sua conta de Serviço de Mídia tem uma cota para o número de entradas de StreamingPolicy. Você não deve criar um novo StreamingPolicy para cada StreamingLocator.

O código a seguir pressupõe que você está chamando a função com um locatorName exclusivo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Ao passo que o exemplo deste tópico discute streaming, você pode usar a mesma chamada para criar um StreamingLocator para fornecimento de vídeo por meio de download progressivo.

### <a name="get-streaming-urls"></a>Obter URLs de streaming

Agora que o [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, você pode obter as URLs de streaming, conforme mostrado no **GetStreamingURLs**. Para criar uma URL, você precisa concatenar o nome de host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho **StreamingLocator**. Neste exemplo, o *padrão* **StreamingEndpoint** é usado. Ao criar uma conta de Serviço de Mídia, este *padrão* **StreamingEndpoint** estará em um estado parado, portanto você precisa chamar **Iniciar**.

> [!NOTE]
> Neste método, é necessário o locatorName que foi usado durante a criação de **StreamingLocator** para a saída do Ativo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Em geral, você deve limpar tudo, exceto os objetos que planeja reutilizar (normalmente, você reutilizará transformações e persistirá StreamingLocators, etc.). Se desejar para sua conta para ser limpa depois de testar, você deve excluir os recursos que não planeja reutilizar.  Por exemplo, o código a seguir exclui trabalhos.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

1. Pressione Ctrl + F5 para executar o aplicativo *EncodeAndStreamFiles*.
2. Copie uma das URLs de streaming do console.

Este exemplo exibe as URLs que podem ser usadas para reproduzir o vídeo usando protocolos diferentes:

![Saída](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testar a URL de streaming

Para testar o streaming, este artigo usa o Player de Mídia do Azure. 

> [!NOTE]
> Se um player estiver hospedado em um site https, atualize a URL para "https".

1. Abra um navegador da Web e navegue até [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:**, cole um dos valores de URL de streaming que você obteve ao executar o aplicativo. 
3. Pressione **Atualizar Player**.

O Player de Mídia do Azure pode ser usado para testes, mas não deve ser usado em um ambiente de produção. 

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente.

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Os SDKs dos Serviços de Mídia do Azure v3 não são thread-safe. Ao desenvolver aplicativos multithread, você deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como carregar, codificar e transmitir seu vídeo, consulte o seguinte artigo: 

> [!div class="nextstepaction"]
> [Analisar vídeos](analyze-videos-tutorial-with-api.md)
