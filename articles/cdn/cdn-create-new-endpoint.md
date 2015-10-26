<properties 
	 pageTitle="Como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure" 
	 description="Este tópico mostra como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="09/01/2015" 
	 ms.author="mazha"/>



#Como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure  

A CDN pode ser habilitada para a origem por meio do Portal de Gerenciamento. Os tipos de origem disponíveis atualmente incluem: Aplicativos Web, armazenamento, Serviços de Nuvem. Você também pode habilitar a CDN para o ponto de extremidade de Streaming dos Serviços de Mídia do Azure. Depois de habilitar um ponto de extremidade da CDN para sua origem, todos os objetos publicamente disponíveis ficam qualificados para o cache de borda da CDN.

Observe que agora você também pode criar uma origem personalizada, e não precisa ser o Azure.

##Para Criar um Novo Ponto de Extremidade CDN  

1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).
2.	No painel de navegação, clique em **CDN**.
3.	Na faixa de opções, clique em **Novo**. Na caixa de diálogo **Novo**, selecione **SERVIÇOS DE APLICATIVO**, **CDN** e, em seguida, em **CRIAÇÃO RÁPIDA**.
4.	Na lista suspensa **TIPO DE ORIGEM**, selecione um tipo de origem na lista de tipos de origem disponíveis.
	
	A lista de URLs de origem disponíveis será exibida na lista suspensa **URL DE ORIGEM**.
	

	![createnew][createnew]

	Se você selecionar **Origem Personalizada**, poderá inserir uma URL de origem personalizada. Não precisa ser uma origem do Azure.

	![customorigin][customorigin]

	>[AZURE.NOTE]Atualmente, apenas HTTP recebe suporte para origens, e você deve usar a extensão de Serviços de Mídia para habilitar a CDN do Azure para um ponto de extremidade de streaming dos Serviços de Mídia do Azure.
	
5.	Clique no botão **Criar** para criar um novo ponto de extremidade.


>[AZURE.NOTE]A configuração criada para o ponto de extremidade não estará imediatamente disponível. Pode levar até 60 minutos para o registro se propagar pela rede CDN. Os usuários que tentarem usar imediatamente o nome de domínio CDN poderão receber o código de status 400 (Solicitação Incorreta) até que o conteúdo esteja disponível por meio da CDN.

##Consulte também
[Como mapear o conteúdo da CDN (rede de distribuição de conteúdo) para um domínio personalizado](cdn-map-content-to-custom-domain.md)

[createnew]: ./media/cdn-create-new-endpoint/cdn-create-new-account.png

[customorigin]: ./media/cdn-create-new-endpoint/cdn-custom-origin.png
 

<!---HONumber=Oct15_HO3-->