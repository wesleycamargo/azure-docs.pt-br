<properties 
	pageTitle="Criar uma conta de Serviços de Mídia - Azure" 
	description="Descreve como criar uma nova conta de Serviços de Mídia no Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#Como criar uma conta de Serviços de Mídia

Este artigo faz parte das séries do [vídeo de serviços de mídia no fluxo de trabalho sob demanda](media-services-video-on-demand-workflow.md) e [fluxo de trabalho de transmissão ao vivo dos serviços de mídia](media-services-live-streaming-workflow.md) .  

O Portal de Gerenciamento do Azure fornece uma maneira de criar rapidamente uma conta de Serviços de Mídia do Azure. Você pode usar sua conta para acessar os Serviços de Mídia que permitem que você armazene, criptografe, codifique, gerencie e transmita conteúdo de mídia no Azure. Quando você cria uma conta de Serviços de Mídia, você também cria uma conta de armazenamento associada (ou usa uma existente) na mesma região geográfica que a conta de Serviços de Mídia. 

Este tópico explica como usar o método de Criação Rápida para criar uma nova conta de Serviços de Mídia e, em seguida, associá-la a uma conta de armazenamento. 

##<a id="concepts"></a>Conceitos
O acesso aos Serviços de Mídia requer duas contas associadas:

-   **Uma conta dos Serviços de Mídia**. Sua conta fornece acesso a um conjunto de Serviços de Mídia baseado em nuvem que está disponível no Azure. Uma conta de Serviços de Mídia não armazena o conteúdo de mídia real. Em vez disso, armazena metadados sobre o conteúdo de mídia e os trabalhos de processamento de mídia em sua conta. No momento em que cria a conta, você seleciona uma região de Serviços de Mídia disponível. A região selecionada é um data center que armazena os registros dos metadados de sua conta. 

    > [AZURE.NOTE]
    > As regiões disponíveis para Serviços de Mídia incluem: **Norte da Europa**, **Europa Ocidental**, **Oeste dos EUA**, **Leste dos EUA**, **Sudeste da Ásia**, **Ásia Oriental**, **Leste do Japão**, **Leste do Japão**. Os Serviços de Mídia não usam grupos de afinidade. 
-   **Uma conta de armazenamento associada**. Sua conta de armazenamento é uma conta de Armazenamento do Azure que está associada à sua conta de Serviços de Mídia. A conta de armazenamento fornece armazenamento de blob para arquivos de mídia e deve estar localizada na mesma região geográfica que a conta de Serviços de Mídia. Quando cria uma conta de Serviços de Mídia, você pode escolher uma conta de armazenamento existente na mesma região ou criar uma nova conta de armazenamento na mesma região. Se você excluir uma conta de Serviços de Mídia, os blobs em sua conta de armazenamento relacionada não serão excluídos. 

##<a id="quick"></a>Como: Criar uma conta de Serviços de Mídia usando a Criação Rápida

1. No [Portal de Gerenciamento][], clique em **Novo**, em **Serviço de Mídia** e em **Criação Rápida**.
   
	![Media Services Quick Create](./media/media-services-create-account/wams-QuickCreate.png)

2. Em **NOME**, digite o nome da nova conta. Um nome de conta de Serviços de Mídia deve ser composto de letras minúsculas ou números, sem espaços, e deve ter de 3-24 caracteres de comprimento. 

3. Em **REGIÃO**, selecione a região geográfica que será usada para armazenar os registros de metadados de sua conta de Serviços de Mídia. Somente as regiões de Serviços de Mídia disponíveis são exibidas na lista suspensa. 

4. Em **CONTA DE ARMAZENAMENTO**, selecione uma conta de armazenamento para fornecer armazenamento de blob do conteúdo de mídia de sua conta de Serviços de Mídia. Você pode selecionar uma conta de armazenamento existente na mesma região geográfica que sua conta de Serviços de Mídia ou criar uma nova conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. 

5. Se você criou uma nova conta de armazenamento, em **NOME DA NOVA CONTA DE ARMAZENAMENTO**, digite um nome para essa conta. As regras para nomes de contas de armazenamento são as mesmas que para contas de Serviços de Mídia.

6. Clique em **Criação Rápida** na parte inferior do formulário.

	Você pode monitorar o status do processo na área de mensagem na parte inferior da janela.

	A página **serviços de mídia ** é aberta com a nova conta exibida. O status alterado para Ativa significa que a conta foi criada com êxito.

	![Media Services Page](./media/media-services-create-account/wams-mediaservices-page.png)

	Quando você clica duas vezes no nome da conta, a página Início Rápido é exibida por padrão. Esta página permite que você execute algumas tarefas de gerenciamento que também estão disponíveis em outras páginas do portal. Por exemplo, você pode carregar um arquivo de vídeo nesta página ou na página CONTEÚDO.

	Além disso, você pode exibir o código que usa o SDK dos Serviços de Mídia do Azure para realizar as seguintes tarefas: carregar, codificar e publicar vídeos. Você pode clicar em um dos links na seção de ESCREVER UM CÓDIGO, copiar o código e usá-lo em seu aplicativo. 


<!-- Reusable paths. -->

<!-- Anchors. -->
  [Conceitos]: #concepts
  [Antes de começar]: #begin
  [Como: Criar uma conta de Serviços de Mídia usando a Criação Rápida]: #quick

<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  
  [Portal de Gerenciamento]: http://manage.windowsazure.com/



<!--HONumber=52-->