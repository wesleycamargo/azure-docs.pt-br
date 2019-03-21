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
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 9c3fbe7cd7ebd41f59be360f40d66b8d38dbce5e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57319271"
---
# <a name="saas-fulfillment-apis"></a>APIs de cumprimento de SaaS

A API de preenchimento SaaS permite que fornecedores de software independentes (ISVs) para integrar seus aplicativos SaaS com o Azure Marketplace. Essa API permite que os aplicativos de ISV participar de todos os canais de comércio eletrônico: direto, conduzida pelo parceiro (revendedores) e o led de campo.  Essa API é um requisito para listagem que transacionáveis SaaS oferece no Azure Marketplace.

A versão atual dessa API é a versão 2, que deve ser usado para todos os SaaS novo oferece.  Versão 1 da API foi preterida e está sendo mantida para oferecer suporte a ofertas existentes.


## <a name="business-model-support"></a>Suporte ao modelo de negócios

Esta API dá suporte os seguintes recursos de modelo de negócios; É possível:

* Especifique vários planos para uma oferta. Esses planos têm funcionalidade diferente e podem ser preços diferentes.
* Fornecer uma oferta em um por site ou por usuário base do modelo de cobrança.
* Fornecer mensal e anual (paga antecipadamente) opções de cobrança.
* Fornece preços privada para um cliente com base em um contrato de negócios negociado.


## <a name="next-steps"></a>Próximas etapas

Use a versão mais recente dessa interface para o desenvolvimento: [Versão de API de preenchimento SaaS 2](./cpp-saas-fulfillment-api-v2.md).
