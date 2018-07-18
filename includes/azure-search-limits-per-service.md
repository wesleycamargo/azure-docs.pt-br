---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b4062aab5a453505ef4586f422a124d4bbf715cb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
ms.locfileid: "30844052"
---
O armazenamento é restrito por espaço em disco ou por um limite rígido no *número máximo* de índices, documentos ou outros recursos de alto nível, o que ocorrer primeiro. A tabela a seguir documenta os limites de armazenamento. Para limites máximos em índices, documentos e outros objetos, consulte [limites por recurso](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Recurso | Grátis | Básico&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| SLA (Contrato de Nível de Serviço) <sup>3</sup>  |Não  |sim |sim |sim |sim |sim |
| Armazenamento por partição |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partições por serviço |N/D |1 |12 |12 |12 |3 |
| Tamanho da partição |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |
| Réplicas |N/D |3 |12 |12 |12 |12 |

<sup>1</sup> Básico tem uma partição fixa. Nesse nível, SUs adicionais são usadas para alocar mais réplicas para cargas de trabalho de consulta maior.

<sup>2</sup> S3 HD tem um limite rígido de três partições, que é menor do que o limite de partição do S3. O limite de partição inferior é aplicado porque a contagem de índice de S3 HD é consideravelmente maior. Uma vez que há limites de serviço para os recursos de computação (processamento e armazenamento) e de conteúdo (índices e documentos), o limite de conteúdo é atingido primeiro.

<sup>3</sup> SLAs (Contratos de Nível de Serviço) são oferecidos para serviços faturáveis em recursos dedicados. Serviços gratuitos e recursos de visualização não têm SLA. Para serviços faturáveis, os SLAs entram em vigor quando você fornece redundância suficiente para seu serviço. Duas ou mais réplicas são necessárias para o SLA de consulta (leitura). Três ou mais réplicas são necessárias para consulta e indexação do SLA (leitura-gravação). O número de partições não é uma consideração de SLA. 