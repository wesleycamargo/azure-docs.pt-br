---
title: APIs de preenchimento SaaS | O Azure Marketplace
description: Apresenta as versões do cumprimento de APIs que permitem que você integre seu SaaS oferece com o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pabutler
ms.openlocfilehash: ec206c2d637f9fb2727d72cf17087050a765672c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941958"
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
