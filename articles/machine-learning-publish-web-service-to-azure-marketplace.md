<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="Publicando serviços Web de AM do Azure para o Azure Marketplace" description="Publicando Serviços Web AM do Azure para o Azure Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/03/2014" ms.author="garye" />

# Publicando Serviços Web AM do Azure para o Azure Marketplace 

Neste documento:

- [Introdução]
- [Visão geral do processo de publicação]
- [Diretrizes para publicar no Azure Marketplace]
- [Opções específicas de Aprendizado de Máquina] 

<!--Anchors-->
[Introdução]: #introduction
[Visão geral do processo de publicação]: #overview-of-the-publishing-process
[Diretrizes para publicar no Azure Marketplace]: #guidelines-for-publishing-to-azure-marketplace
[Opções específicas de aprendizado de máquina]: #machine-learning-specific-options 

## Introdução

O Azure Marketplace fornece a capacidade de publicar serviços Web de Aprendizado de Máquina do Azure como serviços pagos ou gratuitos para consumo por clientes externos. Este documento fornece uma visão geral desse processo com links para diretrizes que o ajudam a começar. Usando esse processo, é possível começar a disponibilizar seus serviços Web a outros desenvolvedores para que usem seus aplicativos.

## Visão geral do processo de publicação 

A seguir estão as etapas para publicar um serviço Web AM do Azure para o Azure Marketplace:

1.	Criar e publicar um serviço Web RSS (serviço de solicitação-resposta) de AM do Azure.
2.	No Portal de Gerenciamento do Azure, implante o serviço na produção.
3.	Use a URL do serviço Web publicado para publicar no Azure Marketplace.
4.	Visão geral do processo de publicação: http://msdn.microsoft.com/pt-br/library/azure/hh580725.aspx 
5.	Depois de enviada, sua oferta é revisada e precisa ser aprovada antes que os clientes possam iniciar a compra dela. O processo de publicação pode levar alguns dias úteis. Estamos trabalhando para reduzir o máximo possível esse tempo e logo forneceremos uma atualização nas comunicações.

## Diretrizes para publicar no Azure Marketplace

1.	Será necessário registrar-se como um editor. Para obter mais informações, consulte: <http://msdn.microsoft.com/pt-br/library/azure/hh563872.aspx>
2.	Será necessário fornecer informações sobre sua oferta, inclusive um plano de preços. Decida se você oferecerá um serviço gratuito ou pago. Para obter mais informações, consulte: <http://msdn.microsoft.com/pt-br/library/azure/hh563873.aspx> 
3.	Para ser pago, será necessário fornecer informações de pagamento como banco e impostos. Para obter mais informações, consulte: <http://msdn.microsoft.com/pt-br/library/azure/hh563873.aspx>

## Opções específicas de Aprendizado de Máquina


1.	Quando criar uma nova oferta, selecione **Serviços de dados**, em seguida, clique em **Criar um novo serviço de dados**. 
 
	![Azure Marketplace][image1]

	<br />

2. Na guia **Serviço de dados**, clique em **Serviço Web** como a fonte de dados.

	![Azure Marketplace][image2]

3.	Obtenha a chave API e o URL do serviço Web do Portal de gerenciamento do Azure:
	1.	Em uma janela separada do navegador ou a guia, faça logon no Portal de gerenciamento do Azure ([https://manage.windowsazure.com](https://manage.windowsazure.com)) 
	2.	Selecione **Aprendizado de máquina** no menu esquerdo
	3.	Clique em **Serviços Web** e depois clique no serviço Web que estiver publicando
	4.	Copie a **chave de API** para um local temporário (por exemplo, Bloco de Notas)
	5.	Clique na **página de ajuda da API** para o tipo de serviço de solicitação/resposta
	6.	Copie o **endereço do ponto de extremidade OData** para o local temporário

	<br />

3.	Na caixa de diálogo Configurar serviço de dados do Marketplace, cole o endereço do ponto de extremidade OData no **URL do serviço**.

	<br />

4. Para Autenticação, escolha **Cabeçalho** como o **Esquema de autenticação**.

	- Insira "Autorização" para o **Nome do cabeçalho**
	- No campo de **Valor do cabeçalho**, insira "Portador" (sem as aspas), depois espaço e, em seguida, cole a chave da API.
	- Marque a caixa de seleção **Este serviço é OData**
	- Clique em **Testar conexão** para testar a conexão

	<br />

5.	Em categorias:
	- Garanta que **Aprendizado de máquina** esteja marcado



[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
