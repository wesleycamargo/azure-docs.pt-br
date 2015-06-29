<properties 
	pageTitle="Publicar o serviço Web de Aprendizado de Máquina ao Azure Marketplace | Microsoft Azure" 
	description="Como publicar o Serviço Web de Aprendizado de Máquina do Azure no Azure Marketplace" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="luisca"/>

# Publicar o Serviço Web do Aprendizado de Máquina do Azure no Azure Marketplace 

O Azure Marketplace fornece a capacidade de publicar serviços Web de Aprendizado de Máquina do Azure como serviços pagos ou gratuitos para consumo por clientes externos. Este artigo fornece uma visão geral desse processo com links para diretrizes que o ajudam a começar. Usando esse processo, é possível disponibilizar seus serviços Web a outros desenvolvedores para que usem seus aplicativos.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Visão geral do processo de publicação 

A seguir estão as etapas para publicar um serviço Web do Aprendizado de Máquina do Azure no Azure Marketplace:

1. Criar e publicar um RRS (serviço de Solicitação-Resposta) do Aprendizado de Máquina
2. Implantar o serviço em produção e obter as informações de ponto de extremidade OData e Chave de API.
3. Use a URL do serviço Web publicado para publicar no [Azure Marketplace (Mercado de Dados)](https://publish.windowsazure.com/workspace/) 
4. Depois de enviada, sua oferta é revisada e precisa ser aprovada antes que os clientes possam iniciar a compra dela. O processo de publicação pode levar alguns dias úteis. 

## Passo a passo
###Etapa 1: criar e publicar um RRS (serviço de Solicitação-Resposta) do Aprendizado de Máquina###
 Se você ainda não fez isso, consulte o [passo a passo](machine-learning-walkthrough-5-publish-web-service.md).

###Etapa 2: implantar o serviço em produção e obter as informações de ponto de extremidade OData e Chave de API.###
1. No [portal de gerenciamento do Azure](http://manage.windowsazure.com), selecione a opção **APRENDIZADO DE MÁQUINA** na barra de navegação à esquerda e selecione seu espaço de trabalho. 

2. Clique na guia **SERVIÇOS WEB** e selecione o serviço Web que você deseja publicar no mercado.

	![Azure Marketplace][workspace]

3. Selecione o ponto de extremidade que você deseja que o mercado consuma. Se não tiver criado pontos de extremidade adicionais, você poderá selecionar o ponto de extremidade **Padrão**.

4. Depois de clicar no ponto de extremidade, você poderá ver a **CHAVE DE API**. Você precisará dessa informação posteriormente na Etapa 3, então, faça uma cópia dela.

	![Azure Marketplace][apikey]

5. Clique no método **SOLICITAÇÃO/RESPOSTA**. nesse ponto, não há suporte a serviços de execução em lote de publicação no Marketplace. Isso o levará para a página de ajuda da API para o método de Solicitação/Resposta.

6. Copie o **Endereço de Ponto de Extremidade OData**. Você precisará dessa informação posteriormente na Etapa 3.

	![Azure Marketplace][odata]




implantar o serviço em produção.



###Etapa 3: usar a URL do serviço Web publicado para publicar no Azure Marketplace (Mercado de Dados)###

1.  Navegue até [Azure Marketplace (Mercado de Dados)](http://datamarket.azure.com/home) 
2.  Clique no link **Publicar** na parte superior da página. Isso o levará para a [Portal de Publicação do Microsoft Azure](https://publish.windowsazure.com)
3.  Clique na seção **editores** para se registrar como um editor.
4.	Quando criar uma nova oferta, selecione **Serviços de dados**, em seguida, clique em **Criar um novo serviço de dados**. 
 
	![Azure Marketplace][image1]

	<br />


5.	Em **Planos**, forneça informações sobre sua oferta, incluindo um plano de preços. Decida se você oferecerá um serviço gratuito ou pago. Para ser pago, forneça informações de pagamento, como banco e impostos.

6.	Em **Marketing**, forneça informações sobre sua oferta, como o título e a descrição da oferta.

7.	Em **Preços**, você pode definir o preço para seus planos para países específicos ou deixar que o sistema defina os preços automaticamente para sua oferta.

8. Na guia **Serviço de Dados**, clique em **Serviço Web** como a **Fonte de Dados**.

	![Azure Marketplace][image2]

9.	Obtenha a chave de API e a URL do serviço Web no portal de gerenciamento do Azure, conforme explicado na etapa 2 acima.

10.	Na caixa de diálogo de configuração do Serviço de Dados do Marketplace, cole o endereço do ponto de extremidade OData na caixa de texto **URL do Serviço**.

11. Para **Autenticação**, escolha **Cabeçalho** como o **Esquema de Autenticação**.

	- Insira "Autorização" para o **Nome do Cabeçalho**.
	- Para o **Valor do Cabeçalho**, digite "Portador" (sem as aspas), clique na barra de **Espaço** e cole a chave de API.
	- Marque a caixa de seleção **Este serviço é OData**
	- Clique em **Testar Conexão** para testar a conexão.

12.	Em **Categorias**, verifique se **Aprendizado de Máquina** está selecionado.

13. Quando você terminar de inserir todos os metadados sobre sua oferta, clique em **Publicar** e em **Enviar por push para preparo**. Nesse ponto, você será notificado de problemas restantes que precisa corrigir.

14. Depois de garantir a conclusão de todos os problemas pendentes, clique em **Solicitar aprovação para enviar por push para produção**. O processo de publicação pode levar alguns dias úteis.


[image1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]: ./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]: ./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 

<!---HONumber=58_postMigration-->