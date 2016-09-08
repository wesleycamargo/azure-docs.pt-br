<properties 
	pageTitle="Como gerenciar conteúdo de mídia com os Serviços de Mídia do Azure usando o Portal Clássico do Azure" 
	description="Saiba como gerenciar seu conteúdo de mídia nos Serviços de Mídia do Azure. Isso inclui: upload, indexação, codificação, criptografia e publicação." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/17/2016"  
	ms.author="juliako"/>


# Gerenciar conteúdo com os Serviços de Mídia do Azure usando o Portal Clássico do Azure


Este tópico mostra como usar o Portal Clássico do Azure para gerenciar o conteúdo de mídia em sua conta dos Serviços de Mídia.

Este tópico mostra como executar as seguintes operações de conteúdo diretamente do portal:

- Exibir informações de conteúdo como estado publicado, URL publicada, tamanho, data e hora da última atualização, e se o ativo está criptografado.
- Carregar novo conteúdo
- Conteúdo do índice
- Codificar conteúdo
- Criptografar
- Publicar/cancelar publicação de conteúdo
- Reproduzir conteúdo


##<a id="upload"></a>Como: carregar conteúdo


[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]


1. No [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), clique em **Serviços de Mídia** e no nome da conta dos Serviços de Mídia.
2. Selecione a página CONTEÚDO.
3. Clique no botão **Carregar** na página ou na parte inferior do portal.
4. Na caixa de diálogo **Carregar conteúdo**, navegue até o arquivo do ativo desejado. Clique no arquivo e, em seguida, clique em **Abrir** ou pressione **Enter**.

	![UploadContentDialog][uploadcontent]

5. Na caixa de diálogo Carregar Conteúdo, clique no botão de seleção para aceitar o Arquivo e o Nome do Conteúdo.
6. O upload é iniciado. Você pode acompanhar o andamento na parte inferior do portal.

	![JobStatus][status]

Quando o upload for concluído, você verá o novo ativo listado na lista Conteúdo. Por convenção, o nome terá "**-Source**" acrescentado ao final para ajudar a acompanhar o novo conteúdo como um conteúdo de origem para tarefas de codificação.

![ContentPage][contentpage]

Se o valor do tamanho do arquivo não for atualizado depois que o processo de carregamento parar, pressione o botão **Sincronizar Metadados**. Isso sincroniza o tamanho do arquivo do ativo com o tamanho real do arquivo no armazenamento e atualiza o valor na página Conteúdo.

##<a id="index"></a>Como: indexar conteúdo

> [AZURE.SELECTOR]
- [.NET](media-services-index-content.md)
- [Portal](media-services-manage-content.md#index)

O Indexador de Mídia do Azure permite que você torne o conteúdo de seus arquivos de mídia pesquisável e gere uma transcrição de texto completo para legendas codificadas e palavras-chave. Você pode indexar seu conteúdo usando o Portal Clássico do Azure, seguindo as etapas demonstradas abaixo. No entanto, se você quiser mais controle sobre quais arquivos e como é feito o trabalho de indexação, você pode usar o SDK dos Serviços de Mídia para .NET ou APIs REST. Para saber mais, consulte [Indexando arquivos de mídia com o Indexador de Mídia do Azure](media-services-index-content.md).

As etapas a seguir demonstram como usar o Portal Clássico do Azure para indexar seu conteúdo.

1. Selecione o arquivo que você deseja indexar.

	Se houver suporte para a indexação para esse tipo de arquivo, o botão PROCESSO será habilitado na parte inferior da página CONTEÚDO.
	
1. Pressione o botão PROCESSO.
2. Na caixa de diálogo **Processo**, escolha o processador do **Indexador de Mídia do Azure**.
3. Em seguida, preencha na caixa de diálogo Processo as informações para **título** e **descrição** do arquivo de mídia de entrada.

![Processo][process]

##<a id="encode"></a>Como: codificar conteúdo

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-asset.md)
- [REST](media-services-rest-encode-asset.md)
- [Portal](media-services-manage-content.md#encode)

Para fornecer vídeo digital pela Internet, você deve compactar a mídia. Os Serviços de Mídia oferecem um codificador de mídia que permite que você especifique como deseja que seu conteúdo seja codificado (por exemplo, os codecs para uso, formato de arquivo, resolução e taxa de bits).

Ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer streaming com uma taxa de bits adaptável aos clientes dos Serviços de Mídia do Azure. Com streaming de taxa de bits adaptável, o cliente pode alternar para um fluxo de taxa de bits maior ou menor, já que o vídeo é exibido com base na largura de banda de rede atual, a utilização da CPU e outros fatores. Os Serviços de Mídia dão suporte às seguintes tecnologias de streaming com taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (apenas para licenciados Adobe PrimeTime/Access).

Os Serviços de Mídia fornecem empacotamento dinâmico, que permite a você distribuir o conteúdo de taxa de bits adaptável MP4 ou Smooth Streaming codificado em formatos de streaming com suporte pelo Media Services (MPEG DASH, HLS, Smooth Streaming, HDS), sem a necessidade de empacotar novamente nesses formatos de fluxo contínuo.

Para aproveitar os benefícios do empacotamento dinâmico, você precisa fazer o seguinte:

- Codificar seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável (as etapas de codificação são demonstradas mais tarde neste tutorial).
- Obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para saber mais, consulte [Como dimensionar unidades reservadas para streaming sob demanda](media-services-manage-origins.md#scale_streaming_endpoints/).

Com o empacotamento dinâmico, você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia criarão e fornecerão a resposta apropriada com base nas solicitações de um cliente.

Além de poder usar os recursos de empacotamento dinâmico, unidades reservadas de streaming sob demanda oferecem capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Por padrão, o streaming sob demanda é configurado em um modelo de instância compartilhada para a qual os recursos do servidor (por exemplo, computação ou capacidade de saída) são compartilhados com todos os outros usuários. Para melhorar a taxa de transferência de um streaming por demanda, é recomendável adquirir unidades reservadas para Streaming por Demanda.

Esta seção descreve as etapas que você pode seguir para codificar o conteúdo com o Codificador de Mídia Padrão usando o Portal Clássico do Azure.

1.  Selecione o arquivo que você deseja codificar.

	Se houver suporte para codificação para esse tipo de arquivo, o botão PROCESSO será habilitado na parte inferior da página CONTEÚDO.

2. Na caixa de diálogo **Processo**, selecione o processador do **Codificador de Mídia Padrão**.
3. Escolha uma das **configurações de codificação**.

	![Process2][process2]


	O tópico [Cadeias de Caracteres Predefinidas de Tarefa para o Codificador de Mídia Standard](https://msdn.microsoft.com/library/mt269960) explica o significado de cada predefinição.

4. Em seguida, digite o nome de conteúdo de saída amigável desejado ou aceite o padrão. Em seguida, clique no botão de seleção para iniciar a operação de codificação e você poderá acompanhar o andamento na parte inferior do portal.
5. Pressione OK.

Após a codificação ser realizada, a página CONTEÚDO conterá o arquivo codificado.

Para exibir o andamento do trabalho de codificação, alterne para a página **TRABALHOS**.

Se o valor do tamanho do arquivo não for atualizado depois que a codificação for concluída, pressione o botão **Sincronizar Metadados**. Isso sincroniza o tamanho do arquivo do ativo de saída com o tamanho real do arquivo no armazenamento e atualiza o valor na página Conteúdo.

##<a id="encrypt"></a>Como: criptografar conteúdo


Se você desejar para os serviços de mídia criptografar dinamicamente seus ativos com uma chave AES ou DRM PlayReady, certifique-se de fazer o seguinte:

- Codificar seu arquivo mezanino (origem) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável (as etapas de codificação são demonstradas mais tarde na seção [Codificar](#encode)
- Obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para saber mais, consulte [Como dimensionar unidades reservadas para streaming sob demanda](media-services-manage-origins.md#scale_streaming_endpoints/).
- Configure a "política de serviço de chave limpa aes padrão" ou "política de serviço sde licença playready padrão". Para saber mais, consulte [Configurar política de autorização de chave de conteúdo](media-services-portal-configure-content-key-auth-policy.md).


	Quando você estiver pronto para habilitar a criptografia, pressione o botão **CRIPTOGRAFIA** na parte inferior da página **CONTEÚDO**.

	![Criptografar][encrypt]

	Uma vez que você habilitou a criptografia, sempre que um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES ou PlayReady. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

Consulte também:

- [Proteger com o PlayReady DRM](media-services-rest-deliver-streaming-content.md)
- [Proteger com a chave AES-128](media-services-protect-with-aes128.md)

##<a id="publish"></a>Como: publicar conteúdo

> [AZURE.SELECTOR]
- [.NET](media-services-deliver-streaming-content.md)
- [REST](media-services-rest-deliver-streaming-content.md)
- [Portal](media-services-manage-content.md#publish)

###Visão geral

Para fornecer a seus usuários uma URL que pode ser usada para transmitir ou baixar seu conteúdo, primeiro você precisa "publicar" o ativo criando um localizador. Os localizadores fornecem acesso aos arquivos contidos no ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizador: OnDemandOrigin, usados para transmitir mídia por streaming (por exemplo, MPEG DASH, HLS ou Smooth Streaming) e SAS (Assinatura de Acesso), usados para baixar arquivos de mídia.

Quando você usa o Portal Clássico do Azure para publicar seus ativos, os localizadores são criados para você e há uma URL com base em OnDemantOrigin (caso seu ativo contenha um arquivo .ism) ou uma URL SAS.

Uma URL SAS tem o seguinte formato:

	{blob container name}/{asset name}/{file name}/{SAS signature}

Uma URL de streaming tem o formato a seguir, e você pode usá-la para reproduzir ativos de Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para criar uma URL de streaming de HLS, anexe (format=m3u8-aapl) à URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para criar uma URL de streaming MPEG DASH, anexe (format=mpd-time-csf) à URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Os localizadores têm uma data de validade. Quando você usa o portal para publicar seus ativos, são criados localizadores com uma data de validade de 100 anos.

>[AZURE.NOTE] Se você usou o portal para criar localizadores antes de março de 2015, foram criados localizadores com uma data de validade de dois anos.

Para atualizar a data de validade em um localizador, use as APIs [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Quando você atualiza a data de validade de um localizador SAS, a URL é alterada.

###Publicar

Para usar o Portal para publicar um ativo, faça o seguinte:

1. Selecione o ativo.
2. Em seguida, clique no botão Publicar.
	
 ![PublishedContent][publishedcontent]


## Como reproduzir conteúdo no portal

O **Portal Clássico do Azure** fornece um player de conteúdo que você pode usar para testar o vídeo.

Clique no vídeo desejado e clique no botão **Reproduzir** na parte inferior do portal.
 
Algumas considerações se aplicam:

- Verifique se que o vídeo foi publicado.
- O **PLAYER DE CONTEÚDO DOS SERVIÇOS DE MÍDIA** é reproduzido por meio do ponto de extremidade de streaming padrão. Se você deseja reproduzir por meio de um ponto de extremidade de streaming não padrão, use outro reprodutor. Por exemplo, o [Player dos Serviços de Mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-manage-content/media-services-portal-player.png

<!---HONumber=AcomDC_0824_2016-->