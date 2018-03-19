---
title: "Arquivo de inclusão"
description: "Arquivo de inclusão"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 4f5dbbd1da8a221fb5f8b9641e016829987db538
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2018
---
Antes de criar qualquer item, vamos revisar o conceito de escopo. O Azure fornece quatro níveis de gerenciamento: grupos de gerenciamento, assinatura, grupo de recursos e recursos. [Grupos de gerenciamento](../articles/billing/billing-enterprise-mgmt-group-overview.md) estão em uma versão prévia. A imagem a seguir mostra um exemplo dessas camadas.

![Escopo](./media/resource-manager-governance-scope/scope-levels.png)

As configurações de gerenciamento são aplicadas em qualquer desses níveis de escopo. O nível que você seleciona determina o quão amplamente a configuração é aplicada. Os níveis inferiores herdam as configurações de níveis superiores. Ao aplicar uma configuração à assinatura, essa configuração será aplicada a todos os grupos de recursos e recursos em sua assinatura. Ao aplicar uma configuração no grupo de recursos, essa configuração será aplicada ao grupo de recursos e a todos os recursos. No entanto, outro grupo de recursos não possuirá essa configuração.

Normalmente, é adequado aplicar configurações críticas em níveis superiores e requisitos específicos do projeto em níveis inferiores. Por exemplo, você pode querer garantir que todos os recursos para sua organização sejam implantados em determinadas regiões. Para cumprir esse requisito, aplique uma política à assinatura que especifica as localizações permitidas. Na medida em que outros usuários em sua organização adicionarem novos recursos e grupos de recursos, as localizações permitidas serão aplicadas automaticamente. 
