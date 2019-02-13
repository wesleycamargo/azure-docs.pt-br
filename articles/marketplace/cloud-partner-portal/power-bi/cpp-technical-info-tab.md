---
title: Informações técnicas para uma oferta de aplicativo do Power BI – Azure Marketplace | Microsoft Docs
description: Configure os campos de informações técnicas para uma oferta de aplicativo do Power BI para o Microsoft AppSource Marketplace.
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
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: d96ef2fd318d6164e1b7dfc5c4b72d6957af0f3e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744427"
---
# <a name="power-bi-apps-technical-info-tab"></a>Guia de informações técnicas sobre aplicativos do Power BI

Na guia **Informações Técnicas** da página **Nova Oferta**, você fornece a URL do pacote do instalador do Power BI e quaisquer informações adicionais necessárias para a validação da nova oferta.  Para a versão inicial, todos os aplicativos do Power BI são gratuitos, estão disponíveis para download das AppSource sem nenhum custo adicional. Como resultado, você não poderá definir nenhuma SKU (unidades de manutenção de estoque) para esse tipo de oferta.

![Guia Informações Técnicas](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campos de Informações Técnicas 

Na guia **Informações Técnicas**, você deve fornecer os campos a seguir.  Um asterisco (*) acrescentado ao rótulo do campo indica que isso é necessário.

|        Campo          |  DESCRIÇÃO                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL do Instalador**     | Endereço gerado pelo Power BI quando você publica o aplicativo e promove-o para produção.  Para obter mais informações sobre como gerar a URL, veja [Publicar aplicativos de serviço no Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Instruções de validação**  |  Instruções de texto opcional (máx. 3.000 caracteres) para a equipe de validação da Microsoft ajudar a configurar, conectar e testar seu aplicativo, incluindo: definições de configuração típicas, contas de teste ou parâmetros que podem ser usados para testar a opção "Conectar Dados" etc. Essas informações só estarão visíveis para a equipe de validação e são usadas somente para fins de validação.  |
| **Este aplicativo é criado como um pacote de conteúdo do Power BI?** | Atualmente, esse é um campo usado internamente. Deixe o valor definido como seu valor padrão, `No`; caso contrário, alterar esse campo para `Yes` pode impedir a publicação.  |  
|  |  |


## <a name="next-steps"></a>Próximas etapas

Na guia [Detalhes da Vitrine](./cpp-storefront-details-tab.md), você fornecerá informações legais e de marketing para seu aplicativo.

