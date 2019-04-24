---
title: Notas de versão do Seller Insights | Microsoft Docs
description: Fornece informações sobre alterações no recurso do Seller Insights.
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
ms.date: 3/3/2019
ms.author: pbutlerm
ms.openlocfilehash: 7df96d53b02e80283f263901dc7e26ed4edb947d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60310988"
---
# <a name="seller-insights-release-notes"></a>Notas de versão do Seller Insights 

(Data de lançamento: 1 de março de 2019)

Este artigo fornece informações sobre as alterações no recurso do Seller Insight no [Portal do Cloud Partner](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Destaques da versão para 1 de março de 2019

* *Tendência de cliente* adicionados ao resumo
* *Principais cinco clientes* no resumo refletir todas as assinaturas do Azure tem um cliente
* *Normalizados Active tendência de pedidos e a tendência de uso* no resumo movido *pedidos mensais em um relance*
* *Relatório de reconciliação de pagamento* atualizado
* *Principais cinco clientes* no pagamento refletem todas as assinaturas do Azure tem um cliente
* *Relatório de uso* atualizado com a ID do cliente
* *Gestão de cliente* em ordens e uso reflete todas as assinaturas do Azure tem um cliente


(Data de lançamento: 28 de julho de 2018)

## <a name="release-highlights-for-july-28-2018"></a>Destaques da versão para 28 de julho de 2018


-   *Preços estimados* fornecem uma exibição dos encargos do cliente com implicações na conversão cambial.
-   *Pagamentos previstos* fornecem uma exibição anterior nos pagamentos potenciais.
-  *Identificadores de referência de uso* fornecem fidelidade de dados entre o uso do cliente e as ordens com pagamentos
-   *Uso em uma granulação diária* fornece mais granularidade e melhores insights sobre o uso do cliente.


### <a name="changes-to-data-structure-and-taxonomy"></a>Alterações na estrutura de dados e taxonomia

A tabela a seguir lista as métricas que foram adicionadas ou substancialmente alteradas com esta versão. 

| **Novo termo**                   |    **Definição**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Preço (CC)                     | Preço de uma unidade de uso para um determinada SKU (na moeda do cliente).       |
| Custo estendido estimado (CC) | Custo estendido estimado pela quantidade de unidades de uso para determinada SKU (na moeda do cliente). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.   |
| Moeda do publicador (PC)        | Moeda preferida pelo publicador para o pagamento.                               |
| Preço estimado (PC)           | Pagamento estimado para uma unidade de uso para determinada SKU com base na conversão cambial na data em que o uso foi calculado (na moeda do publicador). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.   |
| Custo estendido estimado (PC) | Custo estendido estimado para a quantidade de unidades de uso para determinada SKU com base na conversão cambial na data em que o uso foi calculado (na moeda do publicador). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento. |
| Pagamento estimado (PC)          | Pagamento estimado para a quantidade de unidades de uso para determinada SKU com base na conversão cambial na data em que o uso foi calculado (na moeda do publicador). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.   |
| Referência do uso                | O identificador para um ou mais dias de uso do cliente para determinada SKU associada a uma entrada no relatório de pagamento. |
|  |  |
