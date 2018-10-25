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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ec3784a91f8aeb7f0fedd13c9ab86a844832a578
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804972"
---
<a name="seller-insights-release-notes"></a>Notas de versão do Seller Insights 
===============================
(Data de lançamento: 28 de julho de 2018)

Este artigo fornece informações sobre as alterações no recurso do Seller Insight no [Portal do Cloud Partner](https://cloudpartner.azure.com/#insights).


<a name="release-highlights"></a>Destaques da versão
------------------

-   *Preços estimados* fornecem uma exibição dos encargos do cliente com implicações na conversão cambial.
-   *Pagamentos previstos* fornecem uma exibição anterior nos pagamentos potenciais.
-  *Identificadores de referência de uso* fornecem fidelidade de dados entre o uso do cliente e as ordens com pagamentos
-   *Uso em uma granulação diária* fornece mais granularidade e melhores insights sobre o uso do cliente.


<a name="changes-to-data-structure-and-taxonomy"></a>Alterações na estrutura de dados e taxonomia
--------------------------------------

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
