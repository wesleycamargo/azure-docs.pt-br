---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2c16e82ccf259a4cc5ae8fcf35b2dd6b5d50ee2d
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2018
ms.locfileid: "29816927"
---
Ao implantar recursos para o Azure, você terá uma enorme flexibilidade para decidir quais tipos de recursos implantar, onde estarão localizados e como configurá-los. No entanto, essa flexibilidade pode abrir mais opções que você desejaria permitir na sua organização. Ao considerar a implantação de recursos para o Azure, você deve estar se perguntando:

* Como atender aos requisitos legais para a soberania de dados em determinados países?
* Como controlar os custos?
* Como garantir que alguém não altere inadvertidamente um sistema crítico?
* Como fazer para rastrear os custos de recurso e cobrá-los com precisão?

Este artigo aborda essas questões. Especificamente, você:

> [!div class="checklist"]
* Atribui usuários a funções e atribui funções a um escopo, de modo que os usuários tenham permissão para executar as ações esperadas, mas não ações adicionais.
* Aplica políticas que prescrevem convenções para recursos em sua assinatura.
* Bloqueia recursos que sejam críticos ao sistema.
* Marca recursos para que possam ser rastreados por meio dos valores adequados à sua organização.

Este artigo concentra-se nas tarefas realizadas para implementar governança. Para uma discussão mais abrangente sobre os conceitos, consulte [Governança no Azure](../articles/security/governance-in-azure.md). 
