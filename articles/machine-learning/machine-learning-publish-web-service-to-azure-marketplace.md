---
title: "(preterido) Publicar o serviço Web do Machine Learning ao Azure Marketplace | Microsoft Docs"
description: "(preterido) Como publicar o serviço Web do Azure Machine Learning no Azure Marketplace"
services: machine-learning
documentationcenter: 
author: BharathS
manager: jhubbard
editor: cgronlun
ms.assetid: 68e908be-3a99-4cd7-9517-e2b5f2f341b8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: bharaths
ROBOTS: NOINDEX
redirect_url: machine-learning-gallery-experiments
redirect_document_id: TRUE
ms.translationtype: Human Translation
ms.sourcegitcommit: 85a58e09e05fdb50984055ab25c4c2fe520dab4d
ms.openlocfilehash: 2d62966f130f6778c9561393cc7fc338f8903f1e
ms.contentlocale: pt-br
ms.lasthandoff: 01/10/2017


---
# <a name="deprecated-publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>(preterido) Publicar o serviço Web do Azure Machine Learning no Azure Marketplace

> [!NOTE]
> O DataMarket e os Serviços de Dados estão sendo desativados e as assinaturas existentes serão desativadas e canceladas a partir de 31/3/2017. Como resultado, este artigo está sendo preterido. 
> 
> Como alternativa, você pode publicar suas experiências com o Machine Learning na [Galeria do Cortana Intelligence](https://gallery.cortanaintelligence.com/) para o benefício da comunidade de ciência de dados. Para saber mais, confira [Compartilhar e descobrir soluções na Galeria do Cortana Intelligence](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-gallery-how-to-use-contribute-publish).

O Azure Marketplace fornece a capacidade de publicar serviços Web de Aprendizado de Máquina do Azure como serviços pagos ou gratuitos para consumo por clientes externos. Este artigo fornece uma visão geral desse processo com links para diretrizes que o ajudam a começar. Usando esse processo, é possível disponibilizar seus serviços Web a outros desenvolvedores para que usem seus aplicativos.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Visão geral do processo de publicação
A seguir estão as etapas para publicar um serviço Web do Aprendizado de Máquina do Azure no Azure Marketplace:

1. Criar e publicar um RRS (serviço de Solicitação-Resposta) do Aprendizado de Máquina
2. Implantar o serviço em produção e obter as informações de ponto de extremidade OData e Chave de API.
3. Use a URL do serviço Web publicado para publicar no [Azure Marketplace (Mercado de Dados)](https://publish.windowsazure.com/workspace/) 
4. Depois de enviada, sua oferta é revisada e precisa ser aprovada antes que os clientes possam iniciar a compra dela. O processo de publicação pode levar alguns dias úteis. 

## <a name="walk-through"></a>Passo a passo
### <a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Etapa 1: criar e publicar um RRS (serviço de Solicitação-Resposta) do Aprendizado de Máquina
 Se você ainda não fez isso, consulte o [passo a passo](machine-learning-walkthrough-5-publish-web-service.md).

### <a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Etapa 2: implantar o serviço em produção e obter as informações de ponto de extremidade OData e Chave de API.
1. No [Portal Clássico do Azure](http://manage.windowsazure.com), selecione a opção **APRENDIZADO DE MÁQUINA** na barra de navegação esquerda e selecione seu espaço de trabalho. 
2. Clique na guia **SERVIÇOS WEB** e selecione o serviço Web que você deseja publicar no mercado.
   
    ![Azure Marketplace][workspace]
3. Selecione o ponto de extremidade que você deseja que o mercado consuma. Se não tiver criado pontos de extremidade adicionais, você poderá selecionar o ponto de extremidade **Padrão** .
4. Depois de clicar no ponto de extremidade, você poderá ver a **CHAVE DE API**. Você precisará dessa informação posteriormente na Etapa 3, então, faça uma cópia dela.
   
    ![Azure Marketplace][apikey]
5. Clique no método **SOLICITAÇÃO/RESPOSTA**. Nesse ponto, não há suporte a serviços de execução em lote de publicação no marketplace. Isso o levará para a página de ajuda da API para o método de Solicitação/Resposta.
6. Copie o **Endereço de Ponto de Extremidade OData**. Você precisará dessa informação posteriormente na Etapa 3.
   
    ![Azure Marketplace][odata]

implantar o serviço em produção.

### <a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Etapa 3: usar a URL do serviço Web publicado para publicar no Azure Marketplace (Mercado de Dados)
1. Navegue até [Azure Marketplace (Mercado de Dados)](http://datamarket.azure.com/home) 
2. Clique no link **Publicar** na parte superior da página. Isso o levará para a [Portal de Publicação do Microsoft Azure](https://publish.windowsazure.com)
3. Clique na seção **editores** para se registrar como um editor.
4. Quando criar uma nova oferta, selecione **Serviços de Dados**, em seguida, clique em **Criar um novo serviço de dados**. 
   
   ![Azure Marketplace][image1]
   
   <br />
5. Em **Planos** , forneça informações sobre sua oferta, incluindo um plano de preços. Decida se você oferecerá um serviço gratuito ou pago. Para ser pago, forneça informações de pagamento, como banco e impostos.
6. Em **Marketing** , forneça informações sobre sua oferta, como o título e a descrição da oferta.
7. Em **Preços** , você pode definir o preço para seus planos para países específicos ou deixar que o sistema defina os preços automaticamente para sua oferta.
8. Na guia **Serviço de Dados**, clique em **Serviço Web** como a **Fonte de Dados**.
   
    ![Azure Marketplace][image2]
9. Obtenha a chave de API e a URL do serviço Web no Portal Clássico do Azure, como explicado na etapa 2 acima.
10. Na caixa de diálogo de configuração do Serviço de Dados do Marketplace, cole o endereço do ponto de extremidade OData na caixa de texto **URL do Serviço** .
11. Para **Autenticação**, escolha **Cabeçalho** como o **Esquema de Autenticação**.
    
    * Insira "Autorização" para o **Nome do Cabeçalho**.
    * Para o **Valor do Cabeçalho**, digite "Portador" (sem as aspas), clique na barra de **Espaço** e cole a chave de API.
    * Marque a caixa de seleção **Este serviço é OData**
    * Clique em **Testar Conexão** para testar a conexão.
12. Em **Categorias**, verifique se **Machine Learning** está selecionado.
13. Quando você terminar de inserir todos os metadados sobre sua oferta, clique em **Publicar** e em **Enviar por push para preparo**. Nesse ponto, você será notificado de problemas restantes que precisa corrigir.
14. Depois de garantir a conclusão de todos os problemas pendentes, clique em **Solicitar aprovação para enviar por push para produção**. O processo de publicação pode levar alguns dias úteis. 

[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png


