---
title: Atualizar uma oferta do Aplicativo do Power BI existente – Azure Marketplace | Microsoft Docs
description: Atualize uma oferta de Aplicativo do Power BI após ela ter sido publicada no Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 635e2e71bb952aaee761df6a1d5d87c46db531f6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665697"
---
# <a name="update-an-existing-power-bi-app-offer"></a>Atualizar uma oferta de Aplicativo do Power BI existente

Este artigo explica os diferentes aspectos da atualização da sua oferta do Aplicativo do Power BI no [Portal do Cloud Partner](https://cloudpartner.azure.com/) e republica a oferta.  Existem várias razões comuns para você atualizar sua oferta, incluindo:

- Atualizar o conteúdo do aplicativo no Power BI e obter uma nova URL de instalação do aplicativo recém-empacotado
- Atualizar os metadados do marketplace da oferta: vendas, marketing ou informações de suporte e ativos
 
Para ajudá-lo nessas modificações, o portal oferece os recursos **Comparar** e **Histórico**.


## <a name="unpermitted-changes-to-offer"></a>Alterações não permitidas a uma oferta

Há alguns atributos de uma oferta de Aplicativo do Power BI que não podem ser modificados depois que a oferta está ativa no AppSource, principalmente a **ID da Oferta** e a **ID do Publicador**.


## <a name="common-update-operations"></a>Operações comuns de atualização

Embora haja uma grande variedade de características que você pode alterar em uma oferta de Aplicativo do Power BI, as operações a seguir são comuns.


### <a name="update-app-content-in-power-bi"></a>Atualizar o conteúdo do aplicativo no Power BI

É comum que um aplicativo no Power BI seja atualizado periodicamente com novo conteúdo, patches de segurança, recursos adicionais e assim por diante. Em tais cenários, você deseja atualizar a URL para a nova instalação de conteúdo de aplicativos usando as seguintes etapas:

1.  Entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2.  Em **Todas as ofertas**, encontre a oferta para atualizar.
3.  Na guia **Informações Técnicas**, insira uma nova URL do instalador.
4.  Clique em **Publicar** para iniciar o fluxo de trabalho para publicar sua nova versão do aplicativo no AppSource.


### <a name="update-offer-marketplace-metadata"></a>Atualizar metadados do marketplace da oferta

Utilize os seguintes passos para atualizar os metadados do mercado - nome da empresa, logótipos, etc. - associados à sua oferta:

1.  Entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2.  Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3.  Vá até a guia **Detalhes da Vitrine** e siga as instruções na [guia Detalhes da Vitrine de Aplicativos do Power BI](./cpp-storefront-details-tab.md) para realizar alterações nos metadados.
4.  Clique em **Publicar** para iniciar o fluxo de trabalho para publicar suas alterações.


## <a name="compare-feature"></a>Recurso Comparar

Quando você faz alterações em uma oferta já publicada, você pode usar o recurso **Comparar** para auditar as alterações que foram feitas. Para usar esse recurso:

1.  A qualquer momento do processo de edição, clique no botão **Comparar** da sua oferta.

    ![Comparar o botão de recurso](./media/compare-feature-button.png)

2.  Exibir versões lado a lado de ativos de marketing e metadados.


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para ver qualquer atividade de publicação histórica, clique na guia **Histórico** no menu de navegação esquerdo do Portal do Cloud Partner. Aqui, você poderá exibir as ações de data/hora que foram executadas durante o tempo de vida das ofertas do AppSource.


## <a name="next-steps"></a>Próximas etapas

Você deve usar regularmente o recurso [Insights do Vendedor](../../cloud-partner-portal-orig/si-getting-started.md) do [Portal do Cloud Partner](https://cloudpartner.azure.com/#insights) para fornecer insights sobre seu uso e seus clientes do marketplace.  
