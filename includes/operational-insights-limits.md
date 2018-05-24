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
ms.openlocfilehash: 66cd09df128d454973d008adf4ffc5dd1017a18f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
---
Os limites a seguir se aplicam aos recursos do Log Analytics por assinatura:

| Recurso | Limite padrão | Comentários
| --- | --- | --- |
| Número de espaços de trabalho gratuitos por assinatura | 10 | Esse limite não pode ser aumentado. |
| Número de espaços de trabalho pagos por assinatura | N/D | Você está limitado pelo número de recursos dentro de um grupo de recursos e número de grupos de recursos por assinatura | 

>[!NOTE]
>A partir de 2 de abril de 2018 novos espaços de trabalho em uma nova assinatura usará automaticamente o plano de preços *por GB*.  Para assinaturas existentes criadas antes de 2 de abril ou uma assinatura que tenha sido associada a um registro de EA existente, você pode continuar a escolher entre os três tipos de preços para novos espaços de trabalho. 
>

Os limites a seguir se aplicam a cada espaço de trabalho de Log Analytics:

|  | Grátis | Standard | Premium | Autônomo | OMS | Por GB |
| --- | --- | --- | --- | --- | --- |--- |
| Volume de dados coletado por dia |500 MB<sup>1</sup> |Nenhum |Nenhum | Nenhum | Nenhum | Nenhum
| Período de retenção de dados |7 dias |1 mês |12 meses | 1 mês<sup>2</sup> | 1 mês <sup>2</sup>| 1 mês <sup>2</sup>|

<sup>1</sup> Quando os clientes atingirem os seus limites diários de transferência de dados (500MB), a análise de dados será interrompida e reiniciada no início do dia seguinte. Um dia é baseado em UTC.

<sup>2</sup> O período de retenção de dados para planos de preços Autônomo, OMS e Por GB pode ser aumentado para 730 dias.

| Categoria | limites | Comentários
| --- | --- | --- |
| API do Coletor de Dados | O tamanho máximo para uma postagem única é de 30 MB<br>O tamanho máximo para valores de campo é de 32 KB | Dividir volumes maiores em várias postagens<br>Campos com mais de 32 KB são truncados. |
| API de Pesquisa | 5000 registros retornados para dados não agregados<br>500000 registros para os dados agregados | Os dados agregados são uma pesquisa que inclui o comando `summarize`
 
