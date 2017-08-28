---
title: "Introdução ao fornecimento de VoD usando o portal do Azure | Microsoft Docs"
description: "Este tutorial o orienta ao longo das etapas de implementação de um serviço básico de fornecimento de conteúdo de VoD (Vídeo sob Demanda) com o aplicativo AMS (Serviços de Mídia do Azure) usando o Portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: SyntaxC4
editor: 
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: cbb67ef92386a6288b3317bf77ebb67f15ce7fb2
ms.contentlocale: pt-br
ms.lasthandoff: 08/08/2017

---
# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Introdução ao fornecimento de conteúdo sob demanda usando o Portal do Azure
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Este tutorial o orienta ao longo das etapas de implementação de um serviço básico de fornecimento de conteúdo de VoD (Vídeo sob Demanda) com o aplicativo AMS (Serviços de Mídia do Azure) usando o Portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Os itens a seguir são necessários para concluir o tutorial:

* Uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Uma conta dos Serviços de Mídia. Para criar uma conta de Serviços de Mídia, consulte [Como criar uma conta de Serviços de Mídia](media-services-portal-create-account.md).

Este tutorial inclui as seguintes tarefas:

1. Iniciar ponto de extremidade de streaming.
2. Carregar um arquivo de vídeo.
3. Codificar o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável.
4. Publicar o ativo e obter URLs de download progressivo e streaming.  
5. Reproduzir o conteúdo.

## <a name="start-streaming-endpoints"></a>Iniciar pontos de extremidade de streaming 

Ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns o fornecimento de vídeo via streaming de taxa de bits adaptável. Os Serviços de Mídia fornecem um empacotamento dinâmico que permite a você enviar o conteúdo codificado para MP4 da taxa de bits adaptável nos formatos de transmissão suportados pelos Serviços de Mídia (MPEG DASH, HLS, Smooth Streaming) just-in-time, sem ter que armazenar as versões recolocadas de cada um dos formatos de transmissão.

>[!NOTE]
>Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**. 

Para iniciar o ponto de extremidade de streaming, faça o seguinte:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Na janela Configurações, clique em Pontos de extremidade de streaming. 
3. Clique no ponto de extremidade de streaming padrão. 

    A janela DETALHES DO PONTO DE EXTREMIDADE DE STREAMING PADRÃO é exibida.

4. Clique no ícone Iniciar.
5. Clique no botão Salvar para salvar as alterações.

## <a name="upload-files"></a>Carregar arquivos
Para transmitir vídeos usando os Serviços de Mídia do Azure, você precisa carregar os vídeos de origem, codificá-los em várias taxas de bits e publicar o resultado. A primeira etapa é abordada nesta seção. 

1. Na janela **Configuração**, clique em **Ativos**.
   
    ![Carregar arquivos](./media/media-services-portal-vod-get-started/media-services-upload.png)
2. Clique no botão **Carregar** .
   
    A janela **Carregar um ativo de vídeo** é exibida.
   
   > [!NOTE]
   > Não há nenhuma limitação de tamanho do arquivo.
   > 
   > 
3. Navegue até o vídeo desejado no seu computador, selecione-o e clique em OK.  
   
    O carregamento é iniciado e você pode ver o progresso abaixo do nome de arquivo.  

Quando o carregamento for concluído, você verá o novo ativo listado na janela **Ativos** . 

## <a name="encode-assets"></a>Codificar ativos

Ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer streaming com uma taxa de bits adaptável aos clientes dos Serviços de Mídia do Azure. Os Serviços de Mídia permitem as seguintes tecnologias de streaming de taxa de bits adaptável: HLS (HTTP Live Streaming), Smooth Streaming, MPEG DASH. Para preparar os vídeos para a transmissão da taxa de bits adaptável, você precisa codificar o vídeo de origem em arquivos de múltiplas taxas de bits. Você deve usar o codificador **Media Encoder Standard** para codificar seus vídeos.  

Os Serviços de Mídia também fornecem um empacotamento dinâmico que permite enviar seus MP4s de múltiplas taxas de bits nos seguintes formatos de transmissão: MPEG DASH, HLS, Smooth Streaming, sem a necessidade de recolocar nesses formatos de transmissão. Com o empacotamento dinâmico, você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia compilam e fornecem a resposta adequada com base nas solicitações de um cliente.

Para tirar proveito do empacotamento dinâmico, você precisa codificar seu arquivo de origem em um conjunto de arquivos MP4 com múltiplas taxas de bits (as etapas da codificação são demonstradas mais tarde nesta seção).

### <a name="to-use-the-portal-to-encode"></a>Para usar o portal para codificar
Esta seção descreve as etapas que você pode seguir para codificar o conteúdo com o Media Encoder Standard.

1. Na janela **Configurações**, selecione **Ativos**.  
2. Na janela **Ativos** , selecione o ativo que você gostaria de codificar.
3. Pressione o botão **Codificar** .
4. Na janela **Codificar um ativo** , selecione o processador "Media Encoder Standard" e uma predefinição. Para saber mais sobre as predefinições, consulte [Gerar automaticamente uma escada de taxa de bits](media-services-autogen-bitrate-ladder-with-mes.md) e [Predefinições de tarefa para MES](media-services-mes-presets-overview.md). Se você pretende controlar qual predefinição de codificação é usada, lembre-se disso: é importante selecionar a predefinição mais apropriada para seu vídeo de entrada. Por exemplo, se você souber que o vídeo de entrada tem uma resolução de 1920 x 1080 pixels, poderá usar a predefinição "H264 Múltiplas Taxas de Bits 1080p". Se você tiver um vídeo de baixa resolução (640 x 360), não deverá usar a predefinição "H264 Múltiplas Taxas de Bits 1080p".
   
   Para facilitar o gerenciamento, você tem a opção de editar o nome do ativo de saída e o nome do trabalho.
   
   ![Codificar ativos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Pressione **Criar**.

### <a name="monitor-encoding-job-progress"></a>Monitorar o andamento do trabalho de codificação
Para monitorar o andamento do trabalho de codificação, clique em **Configurações** (na parte superior da página), em seguida, selecione **Trabalhos**.

![Trabalhos](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publicar conteúdo
Para fornecer a seus usuários uma URL que pode ser usada para transmitir ou baixar seu conteúdo, primeiro você precisa "publicar" o ativo criando um localizador. Os localizadores fornecem acesso aos arquivos contidos no ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizadores: 

* Localizadores de transmissão (OnDemandOrigin), usados para a transmissão adaptável (por exemplo, para transmitir MPEG DASH, HLS ou Smooth Streaming). Para criar um localizador de transmissão, seu ativo deve conter um arquivo .ism. 
* Localizadores progressivos (SAS), usados para a entrega de vídeo por meio do download progressivo.

Uma URL de streaming tem o formato a seguir e você pode usá-la para reproduzir ativos de Smooth Streaming.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para criar uma URL de streaming de HLS, anexe (format=m3u8-aapl) à URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para criar uma URL de streaming MPEG DASH, anexe (format=mpd-time-csf) à URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Uma URL SAS tem o seguinte formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

> [!NOTE]
> Se você usou o portal para criar localizadores antes de março de 2015, foram criados localizadores com uma data de validade de dois anos.  
> 
> 

Para atualizar uma data de validade em um localizador, use as APIs [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Quando você atualiza a data de validade de um localizador SAS, a URL é alterada.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Para usar o portal para publicar um ativo
Para usar o portal para publicar um ativo, faça o seguinte:

1. Selecione **Configurações** > **Ativos**.
2. Selecione o ativo que você deseja publicar.
3. Clique no botão **Publicar** .
4. Selecione o tipo de localizador.
5. Pressione **Adicionar**.
   
    ![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

A URL é adicionada à lista de **URLs publicadas**.

## <a name="play-content-from-the-portal"></a>Reproduzir conteúdo do portal
O portal do Azure fornece um player de conteúdo que você pode usar para testar o vídeo.

Clique no vídeo desejado e clique no botão **Reproduzir** .

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

Algumas considerações se aplicam:

* Para iniciar o streaming, inicie a execução do ponto de extremidade de streaming **padrão**.
* Verifique se que o vídeo foi publicado.
* Esse **Media player** reproduz do ponto de extremidade de streaming padrão. Se você quiser reproduzir a partir de um ponto de extremidade da transmissão não padrão, clique para copiar a URL e use outra reprodução. Por exemplo, o [Player dos Serviços de Mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


