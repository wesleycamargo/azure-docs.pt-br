---
title: Use a criptografia dinâmica dos Serviços de Mídia do Azure AES | Microsoft Docs
description: Entregue o conteúdo criptografado com chaves de criptografia AES de 128 bits usando os Serviços de Mídia do Microsoft Azure. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados. Este tópico mostra como criptografar dinamicamente com o AES-128 e usar o serviço de distribuição de chaves.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 8d071f64df0097b4029884a9efa84c6f2708fd44
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376594"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Use a criptografia dinâmica AES-128 e o serviço de distrbuição de chaves

Você pode usar os Serviços de Mídia para a distribuição HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming criptografado com o AES usando chaves de criptografia de 128 bits. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados. Se você desejar que os Serviços de Mídia criptografem um ativo, você associa uma chave de criptografia com StreamingLocator e também configura políticas de chave de conteúdo. Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

O artigo se baseia no exemplo [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES). O exemplo demonstra como criar uma transformação de codificação que usa uma predefinição de interna para a codificação de taxa de bits adaptável e recebe um arquivo diretamente de um [URL de origem HTTPs](job-input-from-http-how-to.md). O ativo de saída, em seguida, é publicado usando a criptografia AES (ClearKey). A saída do exemplo é uma URL para o Player de mídia do Azure, incluindo o manifesto DASH e o token AES necessários para reproduzir o conteúdo. O exemplo define a expiração do token JWT para 1 hora. Você pode abrir um navegador e colar a URL resultante para inicializar a página de demonstração do Player de Mídia do Azure com a URL e o token preenchidos para você já no seguinte formato: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

> [!NOTE]
> Você pode criptografar cada ativo com vários tipos de criptografia (AES-128, PlayReady, Widevine, FairPlay). Consulte [Tipos de criptografia e protocolos de streaming](content-protection-overview.md#streaming-protocols-and-encryption-types) para ver o que faz sentido combinar.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir o tutorial.

* Examine o artigo [Visão geral da proteção de conteúdo](content-protection-overview.md).
* Instalar Visual Studio Code ou Visual Studio
* Crie uma nova conta dos Serviços de Mídia do Azure, conforme descrito [neste início rápido](create-account-cli-quickstart.md).
* Obtenha as credenciais necessárias para usar as APIs dos Serviços de Mídia seguindo as [APIs de acesso](access-api-cli-how-to.md)

## <a name="download-code"></a>Código de download

Clone um repositório do GitHub que contenha o exemplo de .NET completo discutido neste artigo em sua máquina usando o comando a seguir:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O exemplo de "Criptografar com AES-128" está localizado na pasta [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES).

> [!NOTE]
> O exemplo cria recursos exclusivos toda vez que você executar o aplicativo. Normalmente, você vai reutilizar recursos existentes como transformações e políticas (se os recursos existentes apresentarem configurações necessárias). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um ativo de saída  

O [Ativo](https://docs.microsoft.com/rest/api/media/assets) de saída armazena o resultado do trabalho de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma transformação de codificação

Ao criar um novo exemplo de [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você precisa especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, como mostrado no código a seguir. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. O exemplo descrito neste artigo usa uma predefinição chamada **AdaptiveStreaming**. A predefinição codifica o vídeo de entrada em uma escada de taxa de bits gerada automaticamente (pares de resolução de taxa de bits) com base na taxa de bits e resolução de entrada e produz arquivos ISO MP4 com vídeo H. 264 e correspondente a cada par de resolução de taxa de bits de áudio AAC. 

Antes de criar uma nova [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você deve verificar se já existe uma usando o método **Get**, conforme mostrado no código a seguir.  Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Enviar Trabalho

Conforme mecionado acima, o objeto de [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como o local da entrada vídeo e o local da saída.

Neste tutorial, criamos a entrada do trabalho com base em um arquivo que é ingerido diretamente de uma [URL de origem HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

Este trabalho levará algum tempo para concluir e quando você desejar ser notificado. O exemplo de código a seguir mostra como sondar o serviço para o status do [Trabalho](https://docs.microsoft.com/rest/api/media/jobs). Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos. Consulte [Rotear eventos para um ponto de extremidade personalizado de web](job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Em fila**, **Processamento**, **Concluído** (o estado final). Se o trabalho encontrou um erro, você receberá o estado do **Erro**. Se o trabalho está no processo de ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Criar um ContentKeyPolicy

Uma chave de conteúdo fornece acesso seguro aos seus ativos. Você precisará criar uma **ContentKeyPolicy** que define como a chave de conteúdo é entregue para os clientes finais. A chave de conteúdo é associada a **StreamingLocator**. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados. 

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES (neste caso, usando criptografia AES.) Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Criar um StreamingLocator

Depois que a codificação for concluída e a política de chave de conteúdo for definida, a próxima etapa é disponibilizar o vídeo no ativo de saída para os clientes o reproduzirem. Para fazer isso em duas etapas: 

1. Crie um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Compile as URLs de streaming que os clientes podem usar. 

O processo de criação de um **StreamingLocator** é chamado de publicação. Por padrão, o **StreamingLocator** é válido imediatamente após você fazer as chamadas de API e dura até ser excluído, a menos que você configure os horários de início e término opcionais. 

Ao criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), você precisará especificar os detalhes desejados **StreamingPolicyName**. Neste tutorial, estamos usando uma das PredefinedStreamingPolicies, que informa como publicar o conteúdo para streaming de Serviços de Mídia do Azure. Neste exemplo, a criptografia de Envelope AES é aplicada (também conhecido como criptografia ClearKey porque a chave é entregue ao cliente via HTTPS e não uma licença do DRM reprodução).

> [!IMPORTANT]
> Ao usar a [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizada, você deve criar um conjunto limitado de tais políticas para sua conta de serviço de mídia e reutilizá-los para o seu StreamingLocators, sempre que a mesmo opção de criptografia e protocolos sejam necessários. Sua conta de Serviço de Mídia tem uma cota para o número de entradas de StreamingPolicy. Você não deve criar um novo StreamingPolicy para cada StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste
        
Neste tutorial, especificamos que a política de chave de conteúdo tenha uma restrição de token. A política restrita do token deve ser acompanhada por um token emitido por um Serviço de Token de Segurança (STS). Os Serviços de Mídia oferecem suporte a tokens no formato [Token Web JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) e isso é o que nós configuramos no exemplo.

O ContentKeyIdentifierClaim é usado em ContentKeyPolicy, o que significa que o token apresentado para o serviço de entrega de chave deve ter o identificador do ContentKey nele. No exemplo, nós não especificamos uma chave de conteúdo ao criar o StreamingLocator, o sistema cria uma senha aleatória para nós. Para gerar o teste de token, devemos obter o ContentKeyId para colocar na declaração ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Criar uma URL de streaming de DASH

Agora que o [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, é possível obter as URLs de streaming. Para criar uma URL, você precisa concatenar o nome de host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho **StreamingLocator**. Neste exemplo, o *padrão* **StreamingEndpoint** é usado. Ao criar uma conta de Serviço de Mídia, este *padrão* **StreamingEndpoint** estará em um estado parado, portanto você precisa chamar **Iniciar**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Em geral, você deve limpar tudo, exceto os objetos que planeja reutilizar (normalmente, você reutilizará transformações e persistirá StreamingLocators, etc.). Se desejar para sua conta para ser limpa depois de testar, você deve excluir os recursos que não planeja reutilizar.  Por exemplo, o código a seguir exclui trabalhos.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>Próximas etapas

Confira como [proteger com DRM](protect-with-drm.md)
