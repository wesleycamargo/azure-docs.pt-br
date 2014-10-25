<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="Publishing Azure ML Web Services to the Azure Marketplace | Azure" description="Publishing Azure ML Web Services to the Azure Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Publicando Serviços Web AM do Azure para o Azure Marketplace

Neste documento:

-   [Introdução][Introdução]
-   [Visão geral do processo de publicação][Visão geral do processo de publicação]
-   [Diretrizes para publicar no Azure Marketplace][Diretrizes para publicar no Azure Marketplace]
-   [Opções específicas de Aprendizado de Máquina][Opções específicas de Aprendizado de Máquina]

<!--Anchors-->

## Introdução

O Azure Marketplace fornece a capacidade de publicar serviços Web de Aprendizado de Máquina do Azure como serviços pagos ou gratuitos para consumo por clientes externos. Este documento fornece uma visão geral desse processo com links para diretrizes que o ajudam a começar. Usando esse processo, é possível começar a disponibilizar seus serviços Web a outros desenvolvedores para que usem seus aplicativos.

## Visão geral do processo de publicação

A seguir estão as etapas para publicar um serviço Web AM do Azure para o Azure Marketplace:

1.  Criar e publicar um serviço Web RRS (Serviço de solicitação-resposta) ou BES (Serviço de execução em lote) AM do Azure.
2.  No Portal de Gerenciamento do Azure, implante o serviço na produção.
3.  Use a URL do serviço Web publicado para publicar no Azure Marketplace.
4.  Visão geral do processo de publicação: <http://msdn.microsoft.com/en-us/library/azure/hh580725.aspx>
5.  Depois de enviada, sua oferta é revisada e precisa ser aprovada antes que os clientes possam iniciar a compra dela. O processo de publicação pode levar alguns dias úteis. Estamos trabalhando para reduzir o máximo possível esse tempo e logo forneceremos uma atualização nas comunicações.

## Diretrizes para publicar no Azure Marketplace

1.  Será necessário registrar-se como um editor. Para obter mais informações, consulte: <http://msdn.microsoft.com/en-us/library/azure/hh563872.aspx>
2.  Será necessário fornecer informações sobre sua oferta, inclusive um plano de preços. Decida se você oferecerá um serviço gratuito ou pago. Para obter mais informações, consulte: <http://msdn.microsoft.com/en-us/library/azure/hh563873.aspx>
3.  Para ser pago, será necessário fornecer informações de pagamento como banco e impostos. Para obter mais informações, consulte: <http://msdn.microsoft.com/en-us/library/azure/hh563873.aspx>

## Opções específicas de Aprendizado de Máquina

1.  Quando criar uma nova oferta, selecione **Serviços de dados**, em seguida, clique em **Criar um novo serviço de dados**.

    ![Azure Marketplace][Azure Marketplace]

2.  Na guia **Serviço de dados**, clique em **Serviços Web** como a fonte de dados.

    ![Azure Marketplace][1]

3.  Para a **URL do Serviço**, use a URL de seu serviço Web:

    -   No menu esquerdo do Estúdio AM do Azure, clique em **SERVIÇOS WEB**.
    -   Clique no serviço Web que deseja publicar no Marketplace.
    -   Na página **Painel**, clique na **Página de ajuda da API** para o serviço RRS.
    -   Copie o endereço do ponto de extremidade OData.

4.  Para Autenticação, escolha **Cabeçalho** como o **Esquema de autenticação**.

    -   Insira “Autorização" para **Nome do cabeçalho**.
    -   Para **Valor do cabeçalho**:

        -   Na página **Painel** de seu serviço Web no Estúdio AM, copie a **Chave da API**.
        -   No campo de **Valor do cabeçalho**, insira “Portador" (sem as aspas), depois espaço e, em seguida, cole a chave da API.
    -   Marque a caixa de seleção **Este serviço é OData**.

5.  Categorias:

    -   Assegure que **Aprendizado de Máquina** esteja marcado.

  [Introdução]: #introduction
  [Visão geral do processo de publicação]: #overview-of-the-publishing-process
  [Diretrizes para publicar no Azure Marketplace]: #guidelines-for-publishing-to-azure-marketplace
  [Opções específicas de Aprendizado de Máquina]: #machine-learning-specific-options
  [Azure Marketplace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
  [1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
