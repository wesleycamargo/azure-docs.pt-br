<properties linkid="manage-services-mediaservices-manage-media-services-content" urlDisplayName="Como gerenciar conteúdo de mídia" pageTitle="Como gerenciar conteúdo de mídia - Serviços de Mídia do Azure" metaKeywords="" description="Saiba como gerenciar seu conteúdo de mídia nos Serviços de Mídia do Azure." metaCanonical="" services="media-services" documentationCenter="" title="Como gerenciar conteúdo nos Serviços de Mídia" authors="migree" solutions="" manager="" editor="" />





# Como gerenciar conteúdo nos Serviços de Mídia #
A exibição de conteúdo dos Serviços de Mídia do Azure permite que você gerencie conteúdo de mídia de sua conta de Serviços de Mídia.

Atualmente, você pode executar as seguintes operações de conteúdo diretamente no portal:

- Exibir informações do conteúdo, como o estado da publicação, a URL publicada, o tamanho e a data e hora da última atualização.
- Carregar novo conteúdo
- Codificar conteúdo
- Reproduzir vídeo do conteúdo
- Publicar/cancelar publicação de conteúdo
- Excluir conteúdo


## Como carregar conteúdo ##


1. No [Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), clique em **Serviços de Mídia** e clique no nome da conta de Serviços de Mídia.
2. Selecione a página CONTEÚDO. 
3. Clique no botão **Carregar** na página ou na parte inferior do portal. 
4. Na caixa de diálogo **Carregar conteúdo**, navegue até o arquivo do ativo desejado. Clique no arquivo e, em seguida, clique em **Abrir** ou pressione **Enter**.

	![UploadContentDialog][uploadcontent]

5. Na caixa de diálogo Carregar Conteúdo, clique no botão de seleção para aceitar o Arquivo e o Nome do Conteúdo.
6. O carregamento será iniciado e você poderá acompanhar o andamento na parte inferior do portal.  

	![JobStatus][status]

Quando o carregamento for concluído, você verá o novo ativo listado na lista Conteúdo. Por convenção, o nome terá "**Source**" acrescentado ao final para ajudar a acompanhar o novo conteúdo como um conteúdo de origem para tarefas de codificação.

Se o valor do tamanho do arquivo não for atualizado depois que o processo de carregamento parar, pressione o botão **Sincronizar Metadados**. Isso sincroniza o tamanho do arquivo do ativo com o tamanho real do arquivo no armazenamento e atualiza o valor na página Conteúdo.	


## Como codificar conteúdo

1. No [Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), clique em **Serviços de Mídia** e clique no nome da conta de Serviços de Mídia. 
2. Clique na página CONTEÚDO na parte superior da página.
3. Clique no vídeo de origem desejado para o trabalho de codificação e, em seguida, clique em **Codificar** na parte inferior da página.
4. Na caixa de diálogo Codificador de Mídia do Azure, escolha uma das predefinições de codificação comuns ou avançadas.

	**Predefinições Comuns**

	+ Reproduzir no PC/Mac (via Flash/Silverlight)**. Essa predefinição produz um ativo de Smooth Streaming com as seguintes características: 44,1 kHz 16 bits/CBR de áudio estéreo de exemplo codificado em 96 kbps usando AAC, e CBR de vídeo de 720 p codificado em 6 bitrates variando entre 3400 kbps a 400 kbps usando o Perfil Principal H.264 e GOPs de dois segundos.
	+ Reprodução por meio de HTML5 (IE/Chrome/Safari)**. Essa predefinição produz um único arquivo MP4 com as seguintes características: 44,1 kHz 16 bits/CBR de áudio estéreo de exemplo codificado em 128 kbps usando AAC, e CBR de vídeo de 720 p codificado em 4500 kbps usando o Perfil Principal H.264.
	+ Reprodução em dispositivos iOS e PC/Mac**. Essa predefinição produz um ativo com as mesmas características que o ativo de Smooth Streaming (descrito acima), mas em um formato que pode ser usado para fornecer fluxos HLS da Apple para dispositivos iOS. 

	**Predefinições Avançadas**
	
	+ O tópico [Cadeias de caracteres predefinidas da tarefa para o Codificador de Mídia do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=270865) explica o que significa cada predefinição na lista de predefinições avançadas. 


	![EncoderDialog][encoder]

	Atualmente, o portal não oferece suporte a todos os formatos de codificação que são suportados pelo Codificador de Mídia. Também não oferece suporte à criptografia/descriptografia de ativos de mídia. Você pode executar essas tarefas programaticamente. Para obter mais informações, consulte [Criando aplicativos com o SDK dos Serviços de Mídia para .NET (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=270866) e [Cadeias de caracteres predefinidas da tarefa para o Codificador de Mídia do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=270865)


5. Na caixa de diálogo Codificador de Mídia do Azure, digite o nome do conteúdo de saída amigável desejado ou aceite o padrão. Em seguida, clique no botão de seleção para iniciar a operação de codificação e você poderá acompanhar o andamento na parte inferior do portal.

Depois da conclusão da codificação, a exibição dever ser semelhante à captura de tela abaixo. 

	![PortalViewUploadCompleted][portaloverview]


Se o valor do tamanho do arquivo não for atualizado depois que a codificação for concluída, pressione o botão **Sincronizar Metadados**. Isso sincroniza o tamanho do arquivo do ativo de saída com o tamanho real do arquivo no armazenamento e atualiza o valor na página Conteúdo.	

## Como publicar conteúdo

1. No [Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), clique em **Serviços de Mídia** e clique no nome da conta de Serviços de Mídia.
2. Clique na página CONTEÚDO na parte superior da página.
3. Clique em um ativo que não está publicado. Clique no botão Publicar para publicar uma URL pública. Quando o conteúdo for publicado em uma URL, a URL poderá ser aberta pelo player cliente que pode renderizar o conteúdo codificado.

 ![PublishedContent][publishedcontent]

## Como reproduzir conteúdo no portal


1. No [Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), clique em **Serviços de Mídia** e clique no nome da conta de Serviços de Mídia.
2. Clique na página CONTEÚDO na parte superior da página.
3. Clique no conteúdo de vídeo desejado e clique no botão **Reproduzir** na parte inferior do portal. Somente conteúdo que tenha sido publicado pode ser reproduzido no portal. Além disso, a codificação deve ser suportada por seu navegador.


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png


[encoder]: ./media/media-services-manage-content/EncoderDialog2.png


