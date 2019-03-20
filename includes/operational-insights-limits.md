---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553254"
---
Os seguintes limites se aplicam aos recursos do Azure Log Analytics por assinatura.

| Recurso | Limite padrão | Comentários
| --- | --- | --- |
| Número de workspaces gratuitos por assinatura | 10 | Esse limite não pode ser aumentado. |
| Número de workspaces pagos por assinatura | Não aplicável | Você está limitado pelo número de recursos dentro de um grupo de recursos e o número de grupos de recursos por assinatura. | 

>[!NOTE]
>A partir de 2 de abril de 2018 novos espaços de trabalho em uma nova assinatura usam automaticamente o *por GB* plano de preços. Para assinaturas existentes criadas antes de 2 de abril ou uma assinatura associada a um registro existente do Enterprise Agreement, você pode continuar a escolher entre três tipos de preço para novos espaços de trabalho. 
>

Os seguintes limites se aplicam a cada espaço de trabalho do Log Analytics.

|  | Gratuito | Padrão | Premium | Autônomo | OMS | Por GB |
| --- | --- | --- | --- | --- | --- |--- |
| Volume de dados coletado por dia |500 MB<sup>1</sup> |Nenhuma |Nenhuma | Nenhuma | Nenhuma | Nenhuma
| Período de retenção de dados |7 dias |1 mês |12 meses | 1 mês<sup>2</sup> | 1 mês<sup>2</sup>| 1 mês<sup>2</sup>|

<sup>1</sup>quando os clientes atingirem os seus 500 MB limite diário de transferência de dados, análise de dados será interrompida e reiniciada no início do dia seguinte. Um dia é baseado em UTC.

<sup>2</sup>o período de retenção de dados para o autônomo, OMS e os planos de preços por GB pode ser aumentado para 730 dias.

| Categoria | Limites | Comentários
| --- | --- | --- |
| API do Coletor de Dados | Tamanho máximo para uma única postagem é de 30 MB.<br>Tamanho máximo para valores de campo é 32 KB. | Dividir volumes maiores em várias postagens.<br>Campos com mais de 32 KB são truncados. |
| API de Pesquisa | 5.000 registros retornados para dados não agregados.<br>500.000 registros para os dados agregados. | Os dados agregados são uma pesquisa que inclui o `summarize` comando.
 
