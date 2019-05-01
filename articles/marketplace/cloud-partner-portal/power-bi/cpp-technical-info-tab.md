---
title: Informações técnicas para uma oferta de aplicativo do Power BI | O Azure Marketplace
description: Configure os campos de informações técnicas para uma oferta de aplicativo do Power BI para o Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 15f4e2a76724a70c15411dea767cc9bc433e4d4a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943227"
---
# <a name="power-bi-apps-technical-info-tab"></a>Guia de informações técnicas sobre aplicativos do Power BI

Sobre o **nova oferta** página, use o **informações técnicas sobre o** guia para fornecer o instalador do Power BI, a URL do pacote e outras informações necessárias validar a nova oferta.  Na versão inicial, todos os aplicativos do Power BI são gratuitos e estão disponíveis para download do AppSource. Por isso, você não pode definir unidades de manutenção de estoque (SKUs) para esse tipo de oferta.

![Na guia informações técnicas](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campos de Informações Técnicas 

Sobre o **informações técnicas** guia, preencha os campos descritos na tabela a seguir. Um asterisco (*) no final de um rótulo de campo significa que o campo é obrigatório.

|        Campo          |  DESCRIÇÃO                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL do instalador\***     | Quando você publica o aplicativo e promovê-lo para produção, o Power BI gera essa URL.  Para obter mais informações, consulte [publicar aplicativos com dashboards e relatórios no Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Instruções de validação**  |  Se você desejar, adicione instruções (até 3.000 caracteres) para ajudar a equipe de validação da Microsoft configurar, conectar e testar seu aplicativo. Inclua definições de configuração típica, contas, parâmetros ou outras informações que podem ser usadas para testar a opção de conectar-se de dados. Essas informações são visíveis apenas para a equipe de validação, e ele é usado apenas para fins de validação.  |
| **Este aplicativo é criado como um pacote de conteúdo do Power BI?** | Atualmente, esse campo é usado apenas internamente. Deixe a configuração padrão de **não**. Se você alterar a configuração para **Sim**, você pode interromper o processo de publicação.  |  
|  |  |


## <a name="next-steps"></a>Próximas etapas

Sobre o [detalhes da vitrine eletrônica](./cpp-storefront-details-tab.md) guia, forneça informações legais e de marketing para seu aplicativo.

