---
title: APIs de preenchimento SaaS - o Azure Marketplace | Microsoft Docs
description: Apresenta as versões do cumprimento de APIs que permitem que você integre seu SaaS oferece com o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 03/26/2019
ms.author: pbutlerm
ms.openlocfilehash: c7da46984d592abc6ed97d7490fde732bf26b0ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798717"
---
# <a name="saas-fulfillment-apis"></a>APIs de cumprimento de SaaS

As APIs de cumprimento de SaaS permitem que fornecedores de software independentes (ISVs) para integrar seus aplicativos SaaS com o Azure Marketplace. Essas APIs permitem que os aplicativos de ISV participar de todos os canais de comércio eletrônico: direto, conduzida pelo parceiro (revendedores) e o led de campo.  Eles são um requisito para listagem transacionáveis ofertas de SaaS no Azure Marketplace.

> [!WARNING]
> A versão atual dessa API é a versão 2, que deve ser usado para todos os SaaS novo oferece.  Versão 1 da API foi preterida e está sendo mantida para oferecer suporte a ofertas existentes.


## <a name="business-model-support"></a>Suporte ao modelo de negócios

Esta API dá suporte os seguintes recursos de modelo de negócios; É possível:

* Especifique vários planos para uma oferta. Esses planos têm funcionalidade diferente e podem ser preços diferentes.
* Fornecer uma oferta em um por site ou por usuário base do modelo de cobrança.
* Fornecer mensal e anual (paga antecipadamente) opções de cobrança.
* Fornece preços privada para um cliente com base em um contrato de negócios negociado.


## <a name="next-steps"></a>Próximas etapas

Se você ainda não fez isso, registre seu aplicativo SaaS na [portal do Azure](https://ms.portal.azure.com) conforme explicado nas [registrar um aplicativo do Azure AD](./cpp-saas-registration.md).  Depois disso, use a versão mais recente dessa interface para o desenvolvimento: [Versão de API de preenchimento SaaS 2](./cpp-saas-fulfillment-api-v2.md).
