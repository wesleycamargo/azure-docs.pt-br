---
title: Configurações de oferta de aplicativo de SaaS do Azure | O Azure Marketplace
description: Defina as Configurações da Oferta para a oferta de aplicativo SaaS no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 6903226ecfe1478b340e390c783c4e57af778f3e
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943535"
---
# <a name="saas-application-offer-settings-tab"></a>Guia Configurações da Oferta de aplicativo SaaS

Esse artigo descreve como definir as configurações de oferta.

A página **Aplicativo SaaS > Nova Oferta** abre com o foco na guia **Configurações da Oferta**. 

Use a guia Configurações de Oferta para configurar a **Identidade da Oferta**, conforme mostrado na próxima captura de tela. Um asterisco (*) anexado ao nome do campo indica que é obrigatório.

![Guia Configurações da Oferta](./media/saas-new-offer.png)

## <a name="offer-identity-settings"></a>Configurações de Identidade de Oferta

Em Oferecer Identidade, forneça as informações para os campos descritos na tabela a seguir. Os campos obrigatórios são indicados por um asterisco (*).

|    Nome do campo      |    DESCRIÇÃO    |
|  ---------------   |  ---------------  |
|  **ID da oferta\***    |  Um identificador exclusivo para a oferta em um perfil de publicador. Essa ID será visível em URLs de produto e relatórios de cobrança. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e está limitada a 50 caracteres no máximo. Observe que esse campo é bloqueado quando uma oferta entra no ar. Por exemplo, se um publicador, Contoso, publicar uma oferta com a ID de oferta sample-vm, ela será exibida no Azure Marketplace como: https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview.                 |
|  **ID do publicador\***    |  A ID do publicador é seu identificador exclusivo no Marketplace. Todas as suas ofertas devem estar anexadas à ID do editor. A ID do Publicador não pode ser modificada depois que a oferta for salva.                |
|  **Nome\***      |   Nome de exibição da oferta. É o nome que aparecerá no Azure Marketplace e no Portal do Azure. Ele pode ter um máximo de 50 caracteres. Inclua um nome de marca reconhecível para o seu produto. Não inclua o nome da sua empresa aqui, a menos que seja a maneira como ela é comercializada. Se você estiver comercializando essa oferta em seu próprio site, certifique-se de que o nome está exatamente como aparece no site.               |
|  |  |

Selecione **Salvar** para salvar o progresso.

## <a name="next-steps"></a>Próximas etapas

[Guia Informações Técnicas](./cpp-technical-info-tab.md)
