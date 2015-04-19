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
	 ms.date="03/05/2015" 
	 ms.author="mazha"/>



#Como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure  

Depois que você habilita um ponto de extremidade CDN para sua origem, como a conta de armazenamento ou o serviço de nuvem, todos os objetos publicamente disponíveis estão qualificados para o cache de borda da CDN.  


##Para Criar um Novo Ponto de Extremidade CDN  

1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).
2.	No painel de navegação, clique em **CDN**.
3.	Na faixa de opções, clique em **Novo**. Na caixa de diálogo **novo**, selecione **Serviços de Aplicativo**, **CDN** e, em seguida, em **Criação Rápida**.
4.	Na lista suspensa **Domínio de Origem**, selecione o destino desejado na lista de tipos de origem disponíveis.
5.	Clique no botão **Criar** para criar um novo ponto de extremidade.




> Observação: A configuração criada para o ponto de extremidade não estará imediatamente disponível. Pode levar até 60 minutos para o registro se propagar pela rede CDN. Os usuários que tentarem usar imediatamente o nome de domínio CDN poderão receber o código de status 400 (Solicitação Incorreta) até que o conteúdo esteja disponível por meio da CDN.

#Consulte Também
[Como Mapear o Conteúdo da CDN (Rede de Distribuição de Conteúdo) para um Domínio Personalizado](cdn-map-content-to-custom-domain.md)

<!--HONumber=49-->